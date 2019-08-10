---
date: 2015-06-06 19:24
title: '[译] 在 Material Design 中表现品牌性'
cover: http://zico.oss-cn-beijing.aliyuncs.com/blog/posts/2017-03-26-0_article_banner.png
excerpt: Material Design 提供了设计实用精美软件的一个系统方法。但如何将你的产品品牌融入到这个设计框架里？这里我们制作了一份在实践 Material Design 的同时保持品牌性的指南。
---

![0_article_banner]({{ site.cdn_url }}/blog/posts/2017-03-26-0_article_banner.png)

> Material Design 提供了设计实用精美软件的一个系统方法。但如何将你的产品品牌融入到这个设计框架里？这里我们制作了一份在实践 Material Design 的同时保持品牌性的指南。

没有人会比你自己更了解自己的品牌。这就是为什么当我们开始着手创造 Material Design 时，我们对第三方开发者使用这个设计系统的方式保持关注。我们希望 Material Design 能给出为设计有效和优雅的用户界面提供一个综合性的指引，尤其关注了手势动画、立体维度、颜色和视觉层级 —— 有足够多的维度实现多种组合。为了方便突出你的品牌特性，你不必将遵循 Material Design 的每一个元素。

不论是自定义字体、独特的颜色、还是不寻常的声音，每一个产品中的差异化风格都是在实践 Material Design 中被认可和支持的。我们列举最重要的几点来阐述下 Material Design 系统的灵活性，并帮助设计师和开发者展现他们的品牌特性。

## Brand personas

![1_app_pesto]({{ site.cdn_url }}/blog/posts/2017-03-26-1_app_pesto.jpg)

**Pesto**: 一个现代私厨应用，帮助你发现、分享和保存菜谱。看起来给年轻人精心设计的。界面设计让人感觉就像在翻阅一本精美的菜谱书。Material Design 关注点：[图标](https://www.google.com/design/spec/style/icons.html)、[图像](https://www.google.com/design/spec/style/imagery.html)、[颜色](https://www.google.com/design/spec/style/color.html)

![2_app_craneair]({{ site.cdn_url }}/blog/posts/2017-03-26-2_app_craneair.jpg)

**Crane Air**: 一个简洁的航班服务的应用。Material Design 关注点：[文字排版](https://www.google.com/design/spec/style/typography.html)

![3_app_pinch]({{ site.cdn_url }}/blog/posts/2017-03-26-3_app_pinch.jpg)

**Pinch**: 一个用来发现和收集激发灵感的图片和项目的应用。Material Design 关注点：[动效](https://www.google.com/design/spec/animation/authentic-motion.html)

![4_app_abisko]({{ site.cdn_url }}/blog/posts/2017-03-26-4_app_abisko.jpg)

**Abisko**: 一个滑雪助手应用，可以帮你检查环境条件、购买机票、订房、直升机等。醒目生动的界面能够吸引运动、滑雪爱好者。Material Design 关注点：[图标](iconography)、[图片](https://www.google.com/design/spec/style/imagery.html)、[颜色](https://www.google.com/design/spec/style/color.html)

![5_app_shrine]({{ site.cdn_url }}/blog/posts/2017-03-26-5_app_shrine.jpg)

**Shrine**: 一个购物应用，受众是年轻的，喜欢DIY，对独立小批量零售感兴趣的人。漂亮的地方在于明亮、轻快、关注产品内容。Material Design 关注点：[图片](https://www.google.com/design/spec/style/imagery.html)、[颜色](https://www.google.com/design/spec/style/color.html)

![6_app_thefortnightly]({{ site.cdn_url }}/blog/posts/2017-03-26-6_app_thefortnightly.jpg)

**The Fortnightly**: 一个精确到分钟级的热点话题的新闻客户端。具有经典、报纸的美感。Material Design 关注点：[字体](https://www.google.com/design/spec/style/typography.html)、[颜色](https://www.google.com/design/spec/style/color.html)

## Make your mark

你的 Logo 可能是在突出品牌特性时，唯一最重要的一个视觉元素。用好它很重要。它不能在界面中太显眼或者干扰用户的交互。作为品牌标识，Logo 最适合使用在启动图标、闪屏和简单地在 App 标题栏上。这是设计中很重要的一个注意点，在设计实践中需要引起关注。

<video loop="true" preload="metadata" autoplay><source src="http://7j1wdn.com1.z0.glb.clouddn.com/assets/images/7_make_your_mark.mp4" type="video/mp4"></video>

**Abisko** 在闪屏中使用 Logo，为整个 App 奠定了一个视觉/体验基调。**Shrine** 在侧滑导航抽屉里使用 Logo，侧滑导航抽屉是最高一级的导航，在这里展现品牌同时也没有干预到产品体验。**Peto** 在显示 Logo 时使用了过渡动画。Logo 在页面中很突出，但当用户开始向下浏览内容时就退居后场了。

> 总结
> * Logo 应该被放在高层级的 UI 元素里
> * Logo 不能影响到用户的交互使用，比如不呢过在 FAB 上
> * Logo 或其他扩展标识应该简洁
> * Logo 可以调整状态已适应用户使用过程，比如一个完整尺寸的Logo可以缩小

## Play to type

[Roboto](https://www.google.com/design/spec/style/typography.html) 是 Android 系统中的默认字体，但不意味着是 Material Design 中的唯一字体。我们相信最好的字体是那些能够体现你的品牌的。字体使得性格特点更丰满。然而，将一个优秀的字体随意地在真个界面设计中运用可能并不能发挥出它的作用。这就是为什么 Material 的字体指南着眼于印刷品设计并非常关注其如何使用 —— 层次、基线网格、透明度、尺寸。

![8_key_typeface]({{ site.cdn_url }}/blog/posts/2017-03-26-8_key_typeface.png)

![9_key_typeface]({{ site.cdn_url }}/blog/posts/2017-03-26-9_key_typeface.jpg)

**Crane Air** 使用明显的字体层次来展现关键信息，使用超大和加粗字体来显示登基信息。而参考基线和尺寸建议依然满足 Material Design 的要求。**The Fortnightly**，使用两种字体分别显示新闻的标题和正文。

```The baseline grid and scale recommendations within material design still apply, regardless of the typeface.```

> 总结：
> * 你可以在 Material Design 中使用任何字体
> * 为品牌建立一套字体使用规范，并遵守它
> * 根据产品/品牌需要来决定你的元素尺寸和信息层次
> * 遵循 Material Design 的基线网格
> * 查看 [typography](https://www.google.com/design/spec/style/typography.html) 了解更多

## Color correct

颜色是品牌标识中最重要的元素之一。想一想 Facebook 的蓝色，UBER 的黑白色主题。如果你已经为你的品牌定义了一个明确的颜色含义，那么遵守它。一个用户如果突然从 UI 上感觉进入了另一个不同的产品是很误导人的。不论你是使用配色板，还是使用自己的颜色运用其中，Material Design 提供了一种简单聪明的方式以构建和谐的颜色。关键在于决定界面运用哪种颜色的方法。

举例来说，Material Design 的配色板，从 500 (Primary Color) 开始然后由浅变深，拥有一套精细挑选的色值。这些标号 500 的颜色很适合用来展现你品牌的主体颜色，同时可以大色块地使用，比如背景和顶部状态栏。以此为基础，你可以选定一个辅助颜色，比如 700 作为系统栏，或者 300 作为二级信息的颜色(Secondary Color)。强调颜色(Accent Color)一般是更明亮且饱和度更高的，在屏幕中其他颜色对比凸显出来，以此引导用户操作。Accent Color 适合用在浮动按钮、按钮、切换开关、和滑块上。

你可以使用很轻松的配合你的品牌色使用这套规范，根据界面元素的重要程度来将元素划分成深浅不一的。挑选一个不错的高对比的强调色用于界面上最主要的操作引导，比如浮动按钮(FAB)。对于内容丰富的品牌，有图片或者很多颜色，可以考虑在界面中使用更加中性或微妙的颜色主题。

![10_key_color]({{ site.cdn_url }}/blog/posts/2017-03-26-10_key_color.jpg)

尽管 The Fortnightly 没有品牌标识上没有颜色，但它同时使用黑色和白色形成层次和对比，其中的颜色主要来自于内容。Abisko 的主体颜色是黑色，与屏幕中亮色和彩色的形成对比。Shrine 的配色包含两种强调色，虽然使用了两种亮色强调色，但白色背景上使用得当获得了平衡良好的对比。

> 总结：
> * 挑选出一个配色板，并遵循
> * 运用对比营造层次，为重要的按钮和信息吸引注意力
> * 不论你的颜色是什么，了解下 Material Design 的方法来帮你运用其中。

## Picture your pages

不论是照片，图例，还是图形处理，如何在品牌之中使用图像是不同的。

如果你有机会，为你的产品设计标识性特征语言。图片、形状以及图标需要统筹一致考虑，不论在表征上相似，还是有意差异化。比如，如果你的产品贯穿着醒目的、高饱和度的图形和图标，那么就要考虑使用一样特征的照片。如果你的应用里，使用了各种各样的图片，那么考虑周边的字体、网格等等其他设计元素如何才能帮助营造视觉一致性。当我们谈论图片时，最重要的事情就是创造一个协调相称的风格，然后明智而审慎地在你的整个UI设计中使用。

![14-article_brand_inline]({{ site.cdn_url }}/blog/posts/2017-03-26-14-article_brand_inline.jpg)

**Abisko** 保持使用像对角斜线等关键的图形元素，以提供一种连续性和活动性。Abisko 中使用的图片与界面中醒目的图形色彩元素相匹配。**Shrine** 在使用大量白底图片的过程中也保持了视觉风格的一贯性。彩色的小小的钻石网格在白底上凸显，避免了界面的过分单调。因为照片是 **Shrine** 中最重要的部分，因此使用大面积贴片，将大部分面积用于图片展示。

> 总结：
> * 考虑一下在产品中使用图片的目的，是信息表达，还是装饰性，或者是为了启发灵感？如果这样，考虑主次关系设计一个方案用于强调出这个目的。
> * 考虑如何使这些视觉元素在你的产品中发挥好作用。不论是风格多样还是表征一致，但确保图片、图标、图形等元素是一个有机组合。
> * 参考 material design 中的 [imagery](https://www.google.com/design/spec/style/imagery.html)。

## Raise your voice

品牌的声音应当在产品体验的所有元素中被承载。从普通的文书，到通知，到错误信息和CTAs（不知道是什么）。如果你的产品就是个性化的，服务于年轻人的，不要让你的通知铃声像是机器人发出的。同样的，你不该在产品中使用行话俚语等，这些会对简单的交互操作都造成阻碍。抓住机会尽可能使用手势和交互模式去引导用户，而不是使用大量的文字说明。记住你是在为品牌创建人物画像。它应该突出你的品牌风格，但不能影响功能的正常使用。

![12_raise_your_voice]({{ site.cdn_url }}/blog/posts/2017-03-26-12_raise_your_voice.png)

**The Fortnightly** 集合了世界上最流行的几家报纸的新闻，因此主要的品牌标准和特点来源于编辑的文章。辅助性的新闻品牌名字清晰明朗，帮助读者理解如何分享和归档阅读材料。**Pesto** 将重点放在了精致有感染力的美味上。信息直接导向那些厨房达人们。**Abisko** 则服务于滑雪重度爱好者们，在产品中间断使用专业术语以清晰地和匹配用户表达。

> 总结：
> * 使用和你的品牌与用户相对于的文字和术语，以树立人户认同感。
> * 展示，而不是告知。在指引用户时，越是能使用手势和交互模式越好，少用文字。
> * 不要过度使用俚语或过于时髦的术语，因为有时候这些会引起用户的交互方式的困惑。

## Go deep & move with meaning

在 Material Design 中，我们提倡以三维的视角去看用户界面，这样可以提供一个清晰的合理的信息层级界面。也帮助用户直观地理解如何操作你的产品。设计中考虑 x、y、z 三维坐标最初可能觉得有点唐突，但考虑信息在操作过程中的如何移动将帮助理解这种独特的交互模式同时保证易用性。交互模式也应当被纳入品牌标识的重要元素。用户会将动态效果、手势操作和你的品牌关联起来，因为这些是产品使用体验的不可分离的部分。比如，向右滑动表示确定，向左滑动表示否定。

UI 中的关键组件决定了产品的外观，并决定了用户在其中交互的方式。比如，FAB（floating action button），表示强调最主要的操作，可以使是写封邮件，可以是添加日历事项。考虑下当用户点击这个按钮时的体验，这很重要。动效如何帮助用户完成一个操作？如何表示操作信息已被收到 —— 使用波纹，按钮浮起贴近触摸点，或者 App bar 在滚动时变换？这些是我们在 Material 世界中的一些方案，很值得在设计中去尝试下。

<video loop="true" preload="metadata" autoplay><source src="http://7j1wdn.com1.z0.glb.clouddn.com/assets/images/13_motion.mp4" type="video/mp4"></video>

**Pinch** 通过核心的交互和手势操作凸显了自己的特征。 **Shrine** 独特的拖拽以及 FAB 的变换效果在醒目的颜色和动效中有意思。FAB 也依旧表示屏幕中最重要的一个操作，而在这个例子里，是拖拽内容触发表面了FAB的用途，而不是使用常规的按钮点击。

> 总结
> * 使用明确的交互模式来帮助定义自己的品牌特性。
> * 越被使用多的交互模式，越需要易于理解。
> * 参考 Material Design 中的 [Motion](https://www.google.com/design/spec/animation/)

Material Design 为创造好的设计和实用的产品提供了基础，但我们鼓励你更大胆和更广泛地想想你的品牌如何搭配上这个设计框架。在 Material Design 的设计准则中运用好产品独特的品牌特性，不论使用颜色、图标、图片、字体、文书、或者交互动画 —— 不仅使你的产品独一无二的风格，也帮助延展了 Material Design 这套设计语言。

EOF

> 翻译地好不地道啊，诸多不对，还望 指正。
> 翻译自：[Expressing Brand in Material](http://www.google.com/design/articles/expressing-brand-in-material/)

