---
title: 在 Android 中使用 Realm 替代 SQLite
date: 2015-07-10 14:35
url: using-realm-to-replace-sqlite-in-android
excerpt: 最近兴起的 Realm，可以用来取代 SQLite 作为数据库吗？实际使用如何。
cover: http://zico.oss-cn-beijing.aliyuncs.com/blog/posts/2017-03-26-realm-sqlite-benchmark.png
---

[Realm](https://realm.io) 是最近兴起的一个专注于移动设备数据库的库，它可以用来取代 SQLite 和其他一些小型数据库，因为其核心是使用 C++ 编写，所以数据的存取速度比 SQLite 要快很多，同时避开了 SQL 相对繁琐的语句编写。最近在一款小 App 里使用了 Realm，总体来说，性能优秀，但由于 Realm 过于 Framework，以至于灵活性比较差，你会觉得很多地方被限制了。

## Realm  对比  SQLite

虽然在 Android 上使用 SQLite 已经有很多开源库（ORMLite、Sugar、greenDAO等等），帮助简化操作，加速开发了。而 Realm 则是建立在 C++ 代码基础上的一个专有数据库 TightDB 上的，其操作非常简单明了，性能优秀。这里我们对比一下，ORMLite 和 Realm 的使用。

### 缓存

如果直接操作 SQLite，我们需要创建类，然后写 SQL 语句，写 SQLDataBaseHelper 去管理，去创建表，去实现增删改查。这会很繁琐。ORMLite 可以简化这些操作，我们可以通过给类名、变量名加注解的方式快速定义表结构。

```Java
@DatabaseTable(tableName = "accounts")
public class Account {

  @DatabaseField(id = true)
  private String name;

  @DatabaseField(canBeNull = false)
  private String password;

  // getters & setters
}
```

在 Realm 中，需要继承 `RealmObject` 类，如此就可以被定义为可持久化。不过 Realm 要求，每个继承自 `RealmObject` 的类，其内部变量必须是 private，必须定义 Getter/Setter，而且 Getter/Setter 中不能处理变量，也就是完全遵循 Java Bean 的特点。

```Java
public class Account extends RealmObject {
  private String name;
  private String password;
  // getters & setters
}
```

### 写入
在 ORMLite 中，需要手动为每一个需要缓存的对象类创建数据表，你需要一个操作器 Dao。

```Java
// if you need to create the 'accounts' table make this call
TableUtils.createTable(connectionSource, Account.class);

// instantiate the DAO to handle Account with String id
Dao<Account, String> accountDao = databaseHelper.getDao(Account.class);

// create an instance of Account
String name = "Jim Smith";
Account account = new Account(name, "_secret");

// persist the account object to the database
accountDao.create(account);
```
Realm 中所有操作来源于 `Realm`，你在执行某个操作的前后，分别调用 `beginTransaction` 和 `commitTransaction`，再期间的操作都会被写入到磁盘。

```Java
realm.beginTransaction();
User user = realm.createObject(User.class); // Create a new object
user.setName("John");
user.setEmail("john@corporation.com");
realm.commitTransaction();
```

或者

```Java
User user = new User("John");
user.setEmail("john@corporation.com");

// Copy the object to Realm. Any further changes must happen on realmUser
realm.beginTransaction();
User realmUser = realm.copyToRealm(user);  
realm.commitTransaction();
```

### 查询
在 ORMLite 中，先获取一个 Dao 对象，然后获得一个 QueryBuilder，使用 where、and、or、eq 来组织查询条件，但可以看到经常会出现大量条件嵌套，阅读上并不直观。

```Java
QueryBuilder queryBuilder = databaseHelper
    .getDao(Account.class).queryBuilder();

Where where = queryBuilder.where();
where.or(
    where.and(
      where.eq("name", "foo"),
      where.eq("password", "_secret")),
    where.and(
      where.eq("name", "bar"),
      where.eq("password", "qwerty")
    )
);
where.query();
```

Realm 则提供了一个更好的API，更符合人的思维过程。

```Java
RealmQuery query = realm.where(Account.class);

query.beginGroup()
        .equalTo("name", "foo")
        .equalTo("password", "_secret")
      .endGroup()
      .or()
      .beginGroup()
        .equalTo("name", "bar")
        .equalTo("password", "_qwerty")
      .endGroup();

query.findAll();
```

所以，Realm 从使用上，相比已经很好的 ORMLite 还是更直观易懂。对于没有太多业务要求的应用来说，完全可以满足取代 SQLite 的需要。最重要的是它的速度惊人，Realm在做查询时的 benchmarks 是 SQLite 的7倍，以至于你可以在本地进行数据库操作，即便是大量缓存数据的查询检索，都可以在 UI 主线程完成，无需担心 ARN，也就避免了多线程操作间可能存在的线程不安全问题。

![realm-sqlite-benchmark]({{ site.cdn_url }}/blog/posts/2017-03-26-realm-sqlite-benchmark.png)

## Realm 的其他特性

Realm 官方还为其与 Gson、Retrofit、otto 等常用开源库的组合使用提供了支持。

Realm 中自带的 RealmBaseAdapter 是带有自动更新数据功能的，也就是RealmResults<? extends RealmObject> 会自动更新，因此 UI 就能得到自动刷新。不过只能跟ListView 配合使用了，要在 RecyclerView 中使用，还是有些麻烦，参见[这篇文章](http://gradlewhy.ghost.io/realm-results-with-recyclerview/)。

## 使用 Realm 的限制

Realm 试图在灵活性和性能之间达成一个平衡，因此，Realm 当前在使用过程中会有不少限制因素。

* Model 类中变量必须是 private
* Getter/Setter 必须是默认方式，也就是不能自定义数据处理
* 不能实现接口，不能是抽象类
* 不能有 static 变量和方法
* 不能被 Serializable，以在 Intent 中传递。（所以只能传个 ID，然后再次查询了，还好查询速度快）
* 由于 Realm 极度线程安全，虽然在不同线程里，都能去访问 Realm "数据库"，但一个线程里的Realms、RealmObject、queries、results 等等，是不能被另一个线程访问的。因此异步查询就不可能了。（比如在一个线程里，我从网络获取了数据，然后以 RealmObject 缓存。然后回到 UI 主线程，此时我要更新刚获得的网络数据，就需要再做一次本地查询。或者就是对于同一个 Model，我创建两个类，内容一致，A1 继承了 RealmObject，A2 不继承。然后 A1 用于存取数据用，A2 用于在 UI 中展示用，然后定义一个 A1 到 A2 的转换。回到这个例子里，就可以在将网络数据，以 A1 形式被 Realm 缓存，以 A2 形式，传递给 UI 线程供刷新UI。不过这样，代码太冗余了。[这篇文章](http://gradlewhy.ghost.io/realm-results-with-recyclerview/)在使用 Realm 和 RecyclerView 时是这么做的）

> 参考：
> Realm : [https://realm.io/docs/java/latest](https://realm.io/docs/java/latest)
> The Making of Falcon Pro 3 : [https://realm.io/news/joaquim-verges-making-falcon-pro-3/](https://realm.io/news/joaquim-verges-making-falcon-pro-3/)

