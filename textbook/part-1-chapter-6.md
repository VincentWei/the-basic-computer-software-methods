# 第 6 章  抽象对象及结构化数据的表述  

## 6.1  语言和区域  

在第 4 章中，我们了解到了计算机如何处理文字。但通过适当的字符集以及编码，并不能解决所有有关语言的问题。比如拿汉语来讲，有繁简体之分，而且同样的名词，可能存在不同的叫法，如计算机“程序”，在台湾称为计算机“程式”，大量的海外地名、人名也有着不同的翻译方法。就算是同样使用英语的国家，在日期表达、货币符号等方面也存在着诸多差异。比如，美国人习惯使用“May 20, 2015”的方式表达日期，而英国人习惯使用“20 May 2015”的方式。  

除此之外，大部分语言在书写的时候使用从左向右的习惯，但阿拉伯、希伯来等语言，使用从右向左的书写习惯[^1]。这一切，都为计算机处理语言相关的问题带来了麻烦。  

最初，计算机系统仅能处理和显示英文，使用 ASCII 字符集基本上就够了。但随着计算机系统在全球各地广泛使用，就需要计算机软件能够根据计算机用户所使用的语言和国家/地区来做适当的处理。典型的就是计算机软件界面的文字需要翻译成特定的语言，而日期、货币等字符串，需要按照当地的习惯来表达。  

为了解决这个问题，计算机系统引入了一个抽象的概念：区域（Locale）。区域定义了一个特定的语言和国家/地区组合，通过这个组合设置，告诉运行在操作系统之上的应用软件，当前的语言是什么，日期、货币等的表达要符合哪个地区的习惯。具体来讲，区域设置对数字、货币、时间和日期的格式化产生影响。比如对英语（美国）这个区域来讲，这些内容的格式化形式示例如下：  

- 数字：123,456,789.00
- 货币：$123,456,789.00
- 时间：4\:58:32 PM
- 短日期：5/20/2015
- 长日期：Wednesday, May 20, 2015  

除了以上用户可以直观感觉到的区别之外，区域还对如下软件系统的接口有影响：  

- 正则表达式处理：如字符（尤其是字母）的排序规则、字符分类、大小写转换等。
- 字符串的本地化处理。  

在 Linux、Windows 等操作系统中，区域的定义通常具有 zh\_CN 这样的形式，其中 zh 是中文/汉语的国际标准代码，CN 是中华人民共和国的国家代码。当计算机操作系统的区域被设置为 zh\_CN 时，系统中的所有软件默认将使用中文简体来显示界面文字，使用“￥”作为货币符号，而日期的表达使用“2015 年 5 月 19 日星期二”这样的形式。如果区域被设置为 zh_TW 时，则使用中文繁体文字，使用中国台湾的习惯用法。  

表 6-1 给出了常见的的语言和国家/地区代码。这个表中给出的语言和国家/地区代码，现在是国际标准，标准代码分别是 ISO 639 和 ISO 3166。我们一般使用这两种代码的短形式（两个字母），有时也使用长形式（三个字母）。  

<small>表 6-1 常见语言和国家/地区编码</small>  

| 语言	    | 语言代码 | 国家/地区 | 国家/地区代码 |
|:----------|:---------|:----------|:--------------|
| 中文/汉语 | zh (zho) | 中国      | CN (CHN)      |
|           |          | 台湾      | TW            |
|	    |          | 香港      | HK            |
|	    |          | 澳门      | MC            |
| 英语      | en (eng) | 美国      | US (USA)      |
|           |          | 英国      | UK            |
| 法语      | fr       | 法国      | FR            |
|           |          |           |               |
| 德语      | de       | 德国      | DE            |
|           |          |           |               |
			
在计算机软件系统中，一些基础的功能模块，尤其是上述数字、货币、时间、日期的格式化功能函数，其行为受区域设置的影响，会根据当前的系统区域设置来相应调整输出。  

在现代操作系统中，一般通过环境变量来设置区域。程序运行时，可继承全局的环境变量，亦可动态修改自己的环境变量。在 Linux 系统中，用于设置区域的环境变量称为 LC\_ALL[^2] 或者 LANG，默认情况下为 en\_US.UTF8 形式，其中的 .UTF8 后缀，指明了文字的字符集及编码。  

另外，具有良好国际化和本地化设计的计算机软件，会根据当前的区域设置来显示不同的界面文字，实现界面文本的自动适配。  

计算机软件的国际化和本地化是两个概念。首先，计算机程序中使用字符串时，要经过一次处理，这个处理通常是一个函数，该函数可根据一个唯一的标识符来返回当前区域对应的字符串，然后再行使用这个字符串。这个处理称为“国际化（internationalization[^3]）”。其次，需要针对不同的区域增加对应的字符串集合，这个过程称为“本地化（localization）”。  

在 Linux 操作系统中，一般直接使用 en\_US 区域下的字符串作为获得本地化字符串的唯一标识符，传递给字符串转换函数，该函数根据当前的区域设置，查找一个映射表，然后返回对应的本地化字符串。我们经常使用 GNU 的 gettext 开源软件完成这项工作，同时使用 msgfmt 工具来完成默认字符串和本地化字符串之间的映射关系，最终生成一个 mo 文件，保存在以区域为名称的子目录（如 zh\_CN）下。若 gettext 无法找到给定区域的字符串映射表时，就会返回原字符串。这样，在没有针对这个区域完成本地化的情况下，程序的输出字符串仍然为原字符串。  

在 Windows 操作系统中，可以使用 GNU 的 gettext 工具实现国际化和本地化，但 Windows 开发工具提供的方案是使用整数的字符串标识符作为字符串对象的唯一标识。  

## 6.2  时间、日期及时区  

在计算机系统中，我们看到的时间和日期是“2015 年 5 月 20 日 18:54”这种形式，然而计算机内部的计时系统并不直接记录上面这种形式的时间和日期，而是以秒为单位的一个整数，这个整数记录了从 1970 年 1 月 1 日 00:00 UTC[^4] 开始到当前时间的秒数，这个秒数被称为“UNIX 时间戳”，而起始的“1970 年 1 月 1 日 00:00 UTC”这个时间被称为 UNIX 纪元时间（UNIX Epoch）。  

操作系统在启动时，首先会从计算机系统的底层硬件时钟当中获得当前的时间，以此为基础换算为 UNIX 时间戳，然后根据时钟芯片产生的滴答中断（一般每秒 100 次）来维护时间戳向前增加。  

在 32 位计算机系统中，操作系统最初维护的时间戳是一个 32 位的无符号整数。经过简单计算我们可知，这个 32 位无符号整数记录的时间戳将在 2038 年的某一天溢出。为提前应对这个问题，现代操作系统的新版本开始使用 64 位无符号整数来记录 UNIX 时间戳，同时也可提供毫秒甚至微秒级的时间信息，以便需要高精度时间数据的应用程序使用。  

“2015 年 5 月 20 日 18:54”这种形式的日期和时间表达方法，在计算机系统中称为“墙钟（Wall clock）”，应用程序可根据操作系统维护的当前时间戳以及时区设置计算出墙钟日期和时间。  

系统时区的设置和上个小节提到的区域类似，使用 TZ 环境变量（在 Linux 操作系统中，若未定义 TZ 环境变量，则使用 /etc/localtime 中的数据），可通过操作系统的编程接口来获得或设置。  

一个给定的时区可以有多种表达方式：  

- 使用标准名称，如北京时间的标准名称为 CST（China Standard Time）。
- 使用标准名称及相对 UTC 的偏移量，其格式为 \<STD>\<OFFSET>。如 CST-8\:00:00，其中 CST 是北京时间的标准名称，-8\:00:00 表示本地时间减去八个小时为 UTC。
- 使用 GMT\<OFFSET> 格式。其中 GMT 为格林威治时间（亦即 UTC），OFFSET 表示对应的本地时间和 GMT 的偏移量。比如 CST 还可以表达为 GMT+8\:00:00。
- 根据所在地区和城市确定时区，比如 Asia/Shanghai 或 America/Chicago 等。  

若给定的时区支持夏令时，会给计算机系统处理本地时间带来更多的麻烦，而且时区的定义可能会发生变化（比如朝鲜就在 2015 年宣布使用自己全新定义的时区，新的朝鲜标准时间比北京时间早 30 分钟，而之前是和北京时间一致的）。为此，现代计算机系统一般会提供最新的时区信息文件，在这些文件中保存有完整的时区描述数据，比如标准名称、UTC 偏移量、是否支持夏令时以及夏令时的起止时间等。在 Linux 系统中，这种文件称为 tzfile；通过使用 TZ 环境变量可指定从特定的 tzfile 中获得对应的时区数据，比如新西兰，使用 TZ=":Pacific/Auckland" 的定义 TZ 环境变量，则对应的时区信息保存在 /usr/share/zoneinfo/Pacific/Auckland 文件中。  

在现代计算机系统中，获取或设置时间戳的接口一般由操作系统内核提供。以 Linux/Unix 为例：  

- time () 函数返回当前的时间戳（以秒为单位）；
- gettimeofday/settimeofday 可以以微秒级精度来获取或设置当前的时间戳；这两个函数亦可用来获得或设置当前的时区[^5]；
- 后来引入的 clock_gettime/clock_settime 函数可以以纳秒级精度来获得或设置当前的时间戳。  

在以上的操作系统内核接口之上，根据时间戳计算得到某个特定时区下的具体日期及时间的功能，则由应用软件负责完成。在不同的编程语言下编程时，相应的平台会提供一些公共接口来帮助应用程序完成日期的计算、对比和转换等工作。比如在使用 Java 语言时，可使用 Calendar 类计算给定时间戳对应的分解时间，具体包括年份、月份、日期、当年第几周、当年第几天等等信息，再结合当前的区域设置，经过字符串的格式化操作，即可得到想要的日期及时间字符串（如“2015-08-27 11:17”）。在使用 C 语言时，C 语言的标准函数库也提供了 ctime、gmtime、localetime、mktime、strftime 等函数来完成时间戳到分解时间的计算，以及时间字符串的格式化。  

当然，我们这里提到的日期都是公历（也称作格列高利历，即 Gregorian Calendar）。如果要显示中国的农历日期，则需要额外的程序执行公历到农历的转换，而这种转换功能并不是软件平台标准的一部分，毕竟这些标准都是西方国家设计的，没几个人懂农历。  

值得一提的是，计算机硬件系统保存的时间和电子/石英手表一样，使用晶振来计时，时间一长，就可能出现大的误差，比如每过 24 小时会快上几秒或者慢上几秒。随着互联网的普及，操作系统开始使用互联网获得更为精确的时间。互联网上有众多提供标准时间的服务器，称为“时间服务器”，通过网络时间协议（NTP）或浮动时间同步协议（FTSP），任何访问这个服务器的系统都可以精确获知当前的时间。这样，很多操作系统在启动时使用上述协议重置系统时间，同时周期性地和时间服务器同步系统时间，以弥补硬件计时系统可能存在的误差。  

## 6.3  HTML  

HTML 是 Web 页面的唯一描述语言，由 Web 发明者 Tim Berners-Lee 于 1989 年发明并逐步演进到今天广为接受的 HTML5。  

在 Web 被发明之前，互联网用户主要通过电子邮件（email）、文件传输协议（FTP）等机制协同工作。在 CERN（欧洲核子研究组织）工作时，Tim Berners-Lee 开发了一个简单的 HTTP 服务器以及一个 WWW（World Wide Web，万维网）客户端程序。这个程序可以从 HTTP 服务器中获得一个 Web 页面文件，然后根据这个文件中定义的标签展示结构化的页面内容，其中包括主题、标题、段落等等。那时候，一个典型的 HTML 页面内容大概如清单 6-1 所示。  

<small>清单 6-1  一个最简单的 HTML 文件内容</small>

```
<HTML>

<HEAD>

<TITLE>
Hello, World!
</TITLE>

</HEAD>

<BODY>
<H1>
HELLO, WORLD!
</H1>

<P>
This is the first Web page in HTML.
</P>

<P>
Click <A href="second.html">HERE</A> to see the second Web page.
</P>

</BODY>

</HTML>
```  
<br>

使用我们现在常用的 Web 浏览器（如 Chrome）渲染上述 HTML 内容，其显示效果如图 6-1 所示。

<div align="center">
  ![一个最简单的 Web 页面渲染效果](illustration/img-6-1.png)<br>
  图 6-1  一个最简单的 Web 页面渲染效果
</div>
<br> 

HTML 是 Hyper Text Markup Language 的缩写，中文译为“超文本标记语言”。HTML 的基础语法来自于 1986 年由 ISO 标准化组织颁布的 SGML（Standard Generalized Markup language，标准通用标记语言）。仔细对比清单 6-1 中的内容和图 6-1 中的显示效果，我们可以看到：  

HTML 中由尖括号（<>）包围的称为标签（tag），标签往往成对出现，定义了一个要显示在页面中的元素（element）。如 \<TITLE> 表示开始定义文档的主题，\</TITLE>表示结束文档主题的定义，这两个标签之间是主题的内容“Hello, World!”。类似地，H1 标签表示的是文档的一个一级标题，P 标签标识的普通段落。如本文档仅包括一个一级标题，其内容为“HELLO, WORLD!”；其后是两个普通段落。  

除了上述具有实质性内容的标记之外，还有 HTML、HEAD、BODY 等定义文档整体结构的标签。其中 HEAD 定义了文档的头部信息，TITLE 标签包含在其中，而 BODY 标签定义了文档的正文内容，所有显示在浏览器页面中的元素定义在 BODY 标签中。  

另外本示例还包含有一个重要的标签 A，该标签定义了超链接（Hyper Link）。在浏览器中，超链接显示为带有下划线的蓝色文字，当用户点击超链接时，将告诉浏览器跳转到另外一个页面。这个页面可能是由同一服务器提供的，也可能是由其他服务器提供的。  

总而言之，HTML 通过标签定义了复杂的 Web 页面的文档结构，这些 Web 页面由浏览器处理并展现，并通过超链接将分布在世界各地的 Web 页面链接在了一起，这是 Google、百度等巨型互联网公司赖以出现并发展壮大的最重要技术基础。  

在之后二十多年的发展中，HTML 从最原始的二十多个标签发展为近百个标签，同时引入了 CSS（级联样式表）技术来灵活定义文档元素的渲染效果，还引入了 JavaScript 编程语言来动态控制页面的内容。随着 HTML5 标准的正式发布，硬件处理性能的提高，大部分人们开始倾向于认为未来将是 Web App 的天下；2015 年，由优秀华人计算机专家宫力博士发起创立的 Acdemic 公司，开始面向全球开发和推广基于 HTML5 技术的操作系统：H5OS。这一切预示着 HTML5 及其相关标准和技术在未来可能具有不可限量的发展空间。为此，本书将分别在第五篇“信息的计算机展现”和第六篇“计算机编程语言”中专门讲述使用 HTML5、CSS3、JavaScript 相关技术来开发 Web 应用的基本概念及方法。  

在今天我们看来，互联网之所以获得巨大成功，和 Web 以及相关技术（如 HTML）的发明有着绝对的直接关系。尽管 Tim Berners-Lee 并没有从 Web 的发明中获得直接利益，但时至今日，他仍然作为 W3C（万维网联盟，Web 相关标准的制定者）发起人和主席参与相关标准的制定，指挥着万维网的发展。  

### 6.3.1  HTML 的演进  

HTML 从第一个版本到现今广为接受的 HTML5，走过了二十多年的历程，期间出现了许多 HTML 的版本，也出现了很多相关的标准或规范[^6]。有意思的是，尽管这些标准基本上都是出自 W3C 之手，但有些标准和规范并未获得业界的广泛接受。更有意思的是，就算是同一个标准，很多厂商（主要是浏览器厂商）并没有按照统一的标准和规范要求做相应的实现，有些厂商甚至刻意在其软件实现（浏览器）中引入了不兼容。
比如 XHTML，这个标准被戏称为 HTML 的 XML 版本；XHTML 和 HTML 并没有本质上的区别，但 XHTML 要求按照 XML 的语法来严格书写 HTML，比如标签和属性必须使用小写，属性值必须使用双引号（"）包围，空标签必须使用 \<hr /> 这样的形式，不能省略结束标签（如 \</p>），不能随意嵌套元素等等。然而，XHTML 并未被广泛接受，因为虽然有大量的网页并不符合这个标准的要求，但浏览器仍然可以正常处理而不会报任何错误，于是，没有开发者愿意花额外的时间来做检查并修复不符合规范要求的写法，况且很多 HTML 代码是由程序自动生成的，还涉及到程序的修改，其工作量可想而知。之所以提出 XHTML 这个标准，现在看来，大概是因为标准制定者的心理洁癖。目前，W3C 仍然致力于 XHTML 标准的演进，在颁布新的 HTML 标准之时，都会相应颁布对应的 XHTML 版本。比如HTML 4.01 对应的是 XHTML 1.0；HTML5 对应的是 XHTML5。  

现代浏览器在处理从 HTTP 服务器端获得的网页时，根据内容类型（Content Type）来决定使用 HTML 的规范要求还是使用 XHTML 的规范要求。当服务器返回的内容类型为 text/html 时，浏览器按照 HTML 处理，不要求严格的语法，不会报任何语法错误；当内容类型为 application/xhtml+xml 时，则按照 XHTML 处理，浏览器会做严格的语法检查，存在任何不符合 XML 语法要求的情况均会报错。这大概是一种折衷处理办法，毕竟从长远看，符合标准的严格语法要求有利于长期发展，但面对现实情况又不得不做出让步。  

在 HTML 4.0 提出之前比较成熟的标准是 HTML 3.2，该版本颁布于 1997 年 1 月。HTML 3.2 及之前的版本主要使用某些格式标签，如 font（字体）、big（大字体）、strike（删除线），以及一些属性，如 color（颜色）等定义文本的样式变化。但这种标签和属性的大量使用一方面导致 HTML 代码看起来非常混乱，另外一方面和 HTML 仅定义文档结构的初衷相背离。为解决这一问题，W3C 逐步引入了 CSS 的概念。  

使用 CSS，我们可以将文档中的元素和该元素对应的渲染样式隔离开来。具体来讲，就是我们可以针对 HTML 文档中的某个特定元素或某类元素单独定义其渲染样式。一个 CSS 样式可定义一个元素的尺寸、边框、背景、前景色、字体、对齐方式等等样式属性，而且可以单独存放在 CSS 文件中。CSS 的发明给 Web 开发带来了非常深远的影响，大受 Web 开发者的欢迎。  

然而，真正成熟和广泛接受的 HTML 4.01 版本是在 1999 年颁布的，对应的 CSS 版本是 CSS 2.0。在这之前，从 HTML 3.2 开始，CSS 1.0/1.2 已经得到一些浏览器的支持，好在并没有太多开发者使用，那时用户使用的浏览器绝大部分是微软的 IE（Internet Explorer），因此，向更高版本标准的演进并没有导致太多的麻烦。遇到真正的麻烦的是 CSS 3.0。CSS 3.0 引入了更加清晰的布局概念，同时也引入了 CSS 动画支持。从 CSS 2.0 向 CSS 3.0 演进的过程，刚好是 IE、FireFox、WebKit、Opera 等浏览器混战的年代，于是，在 CSS 3.0 标准的草案还在讨论当中时，这些浏览器就开始部分或者全部支持了 CSS 3.0 的特性，于是，我们看到很多 CSS 样式表中包含这样的写法：  

```
    -webkit-box-shadow:0 2px #135389;
    -moz-box-shadow:0 2px #135389;
    -o-box-shadow:0 2px #135389;
    box-shadow:0 2px #135389;
```  
<br>

上面这四行其实是在重复一个属性的定义，不过前三行具有不同的前缀，对应于不同的浏览器：WebKit、Mozilla（FireFox）、Opera。等到 HTML5 标准颁布并获得这些浏览器的支持之后，只要保留最后一行就可以了。  

上面的例子只是一个简单的例子，真正令开发者头疼的是浏览器在某些方面的特性支持不一致带来的问题。比如，由于 Windows 操作系统的垄断地位，微软在 Internet Explorer 的开发中就引入了很多不符合标准的特性，最为著名的就是 ActiveX 控件。ActiveX 控件使用 Windows 平台特有的接口实现，如果某个网页使用了 ActiveX 控件，就无法在运行于 Unix/Linux 平台上的浏览器上正常展示或正常工作，最常见的就是各种网络银行网站。在最新的 W3C 标准的支持程度上，微软的 IE 始终比其他浏览器的表现差很远。当然，这给 FireFox、Opera、Chrome 等浏览器以机会来抢占市场份额。不过对 Web 开发者而言，Windows 平台上的 IE 浏览器是不能被轻易忽略的——因为其长期以来占据的显著市场份额。这导致了 Web 开发成本的上升，也阻碍了新标准的实施。  

HTML 标准的演进过程是一个典型的计算机软件标准发展过程，它反映了标准演进过程一些经常遇到的问题：  

1) 标准无法具有足够的前瞻性，总是很难及时满足飞速发展的市场需求，这导致大部分标准形成于事实存在之后，如 CSS 以及 HTML5 新增的画布标签等等。
2) 某些势力会利用其垄断地位刻意在产品中引入和标准不兼容的特性，如 IE 中的 ActiveX 控件；另外，旧势力（如微软 IE）的存在，会极大阻碍新标准的推广。  

另外，技术的发展会让某些技术变得过时或者被淘汰，比如，很早出现的 Java Applet 技术最终被 JavaScript 取代，而 HTML5 多媒体标签的出现让 Adobe Flash 插件变得过时。  

### 6.3.2  HTML5  

1999 年颁布的 HTML 4.01 可以说是 Web 开发中的一个重大转折点，这主要得益于 CSS和 JavaScript 技术的成熟。使用 HTML 4.01 开发的 Web 应用，可以获得更美观的排版效果以及动态特性。然而，HTML 4.01 仍然存在一些不足：  

- DIV 标签的滥用。为了和 CSS 配合形成美观的排版布局效果，大部分网页使用了大量无意义的 DIV 标签。这背离了 HTML 仅定义文档结构的出发点。
- 在网页中嵌入音频、视频播放器时，仍然要通过插件来进行，比如使用 Adobe Flash 来播放视频。这需要用户在浏览器之外安装额外的插件，同时增大了 Web 开发的工作量。
- 仅仅操作 HTML 文档内容，无法开发出精细的二维或者三维图形应用，比如大型网游。当然，开发者可以自行开发插件来提供相应的功能，但一旦开发自己的插件，就要针对不同的浏览器开发出对应的插件版本。
- 仅仅依赖于 HTTP 协议，很难开发实时联网类应用，如实时聊天室。这里需要说明的是，HTTP 本质上是一种无状态的短连接协议。  

本质上，HTML5就是为了解决上面这些问题而出现的。比较有意思的是，从 HTML 4.01 到 HTML5，中间经过了漫长的十五年！中间这段时间 W3C 干嘛去了呢？  

这和之前提到的标准制定者的心理洁癖有关。颁布了 HTML 4.01 之后，W3C 采取了一个错误的路线，一心想把 Web 开发向 XML 方向上引导，所以制定了 XHTML 1.0、1.1、2.0 等标准和规范。尤其是 XHTML 2.0，这个版本一方面更加 XML 化，另一方面和之前的 XHTML 1.0/1.1 都不兼容，这显然是在闭门造车嘛。所以浏览器厂商不买 W3C 的账（其实开发者也不买 W3C 的账），于是浏览器厂商在 Opera 的牵头下另起炉灶办了一个超文本应用技术工作组，也就是 WHATWG。WHATWG 向后来的 HTML5 演进，而 W3C 则在错误的路线孤注一掷。  

2006 年，W3C 主席（Tim Berners-Lee）认识到了 W3C 的错误，开始主动向 WHATWG 示好，并在 WHATWG 的工作基础上组建了一个新的 HTML 工作组。2009 年，W3C 宣布终止在 XHTML 方向上的工作。  

虽然有过几年的时间，WHATWG 和 W3C 分头向 HTML5 演进，但最终两家合在了一起。2014 年 10 月 28 日，HTML5 正式成为 W3C 的推荐标准。  

HTML5 的主要特点如下：  

- 在 HTML 4.01 基础上废弃了一些用于定义样式和格式的标签或属性，进一步强化 HTML 仅用于定义文档结构的特征。废弃的标签有 basefont（基础字体）、big（大字体）、center（居中）、font（字体）、s（小字体）、strike（删除线）、u（下划线）、frames（框架）、frameset（框架集）、noframe（无框架）等。废弃的属性有 align（水平对齐）、bgcolor（背景色）、height（高度）、width（宽度）、valign（垂直对齐）等。
- 新增 article（文章）、header（页眉）、footer（页脚）、section（小节）、nav（导航）、aside（边栏）、hgroup（标题组）等标签，可以用于定义文档中的不同类型的段落或者页面区域，这些标签的恰当使用，同时可以防止 div 标签的滥用。
- 新增 canvas（画布）标签，Web 应用可使用 JavaScript 和 WebGL 接口在指定的画布上绘制二维或者三维图形。
- 新增 audio（音频）、video（视频）标签，Web 应用可在网页中嵌入音频和/或视频播放器。
- 通过 JavaScript API 提供了本地存储相关接口。Web 应用可在浏览器端保存数据；键值对形式或者数据库形式。
- 通过 JavaScript API 提供了地理位置相关接口，Web 应用可获知用户所在的地理位置信息。
- 通过 JavaScript API 提供了套接字支持，从而使得 Web 应用可以和服务器端建立持久的套接字连接（区别于基于 HTTP 的无状态短连接）。
- 支持离线 Web 应用。
- 为表单元素提供了更好的类型支持和本地检测机制。  

显然，HTML5 标准制定者的目标很明显，通过相关标准和规范的实施，基于 HTML5 的 Web 应用最终将不仅仅限于展示网页：它本可以开发各种各样的，你能想象到的所有的应用程序。  

作为本节的结束，我们看看清单 6-1 中的文档用 HTML5 规范书写是什么样子的。见清单 6-2。而有关 CSS 和 JavaScript 相关的内容，将在本书第五篇“信息的计算机展现”和第六篇“计算机编程语言”中讲述。  

<small>清单 6-2  一个简单网页的 HTML5 版本</small>  

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <title>Hello, World!</title>
</head>

<body>
<article>
    <header>
        <h1>HELLO, WORLD!</h1>
    </header>

    <section>
      <p>This is the first Web page in HTML5.</p>
      <p>Click <a href="second.html">HERE</a> to see the second Web page.</p>
    </section>
</article>
</body>
</html>
```
<br>

## 6.4  XML  

XML（eXtensible Markup Language，可扩展标记语言）也是由 W3C 颁布、维护和发展的标准。XML 最早于 1998 年 2 月成为 W3C 的推荐标准。和 HTML 类似，XML 也可看成是 SGML 的子集；但和 HTML 不同的是，XML 主要用来表述复杂的结构化数据，比如某个行政单位的组织机构或者人员清单以及人员特性，如年龄、性别、学历等等。另外，HTML 由浏览器解析并展现，而 XML 的处理更为灵活，任何程序都可以利用 XML 来存储、传输自己的结构化数据。  

XML 和 HTML 有如下不同：  

- XML 不是用来替代 HTML 的，但可以看成是 HTML 的补充，主要用来存储和传输结构化数据，其表述方式和特定的软件或硬件系统无关。
- XML 的标签不是预先定义好的，而需要由用户自行定义。  

除此之外，XML 具有严格的语法，HTML 则更加灵活。比如清单 6-1 中，我们可以省去两个 </P> 标签，浏览器仍然能够正常处理并展示其内容，但 XML 却不行。  

由于 XML 的灵活性，加上是 W3C 的推荐国际标准，因此，XML被广泛应用于不同的场合，出现在各种各样的软件中。  

### 6.4.1  XML 示例  

清单 6-3 给出了一个典型的 XML 文件，用来定义一个数据库表的字段。  

<small>清单 6-3  一个定义数据库表格的 XML 文件</small>  

```
<?xml version="1.0" encoding="UTF-8"?>
<schema version="0.3">
        <table name="foo_bar">
                <field name="id" type="I">
                        <key />
                        <unsigned />
                </field>
                <field name="foo" type="I">
                        <notnull />
                        <default value="0" />
                </field>
                <field name="bar" type="C" size="32">
                        <notnull />
                        <default value="" />
                </field>
        </table>
</schema>
```  
<br>  

和 HTML 类似，XML 使用标签（tag）来定义一个元素（也称为节点或对象），通过标签的层级关系来定义元素之间的结构，另外，标签中可以定义一个或者多个属性（attribute）。  

清单 6-3 给出的 XML 文件中，第一行相当于是 XML 文件的文档类型签名，以方便程序或开发者识别这是一个 XML 文件，其中包含了 XML 标准版本号以及该文件的字符编码方式（通常就是 UTF-8）。第二行定义了一个 schema 标签，这是这个 XML 文件的根元素，类似 HTML 文件中的 html 标签；每个 XML 文件只能包含一个根元素。随后，该文件使用 table 标签定义了一个数据库表格，其名称为 foo\_bar（使用 table标签的 name 属性定义），在 table 标签内，使用 field 标签定义了三个字段，以表明字段是表格的子元素。类似地，通过 field标签的属性定义了三个字段的名称、类型等特性[^7]：  

- 字段的名称使用 field 标签的 name 属性定义。
- 字段的数据类型使用 filed 标签的 type 属性定义，其值为 I 表示是整数类型，其值为 C 表示是字符类型。当类型为 C 时，使用 size 属性定义其长度。比如上述示例定义的 id 字段是整数类型，foo 字段也是整数类型，但 bar 字段是字符类型，长度为 32。  

需要注意的是，上述示例 XML 文件中，filed 标签中还包含有 key、unsigned、notnull、default 等子标签。理论上讲，这些子标签仍然定义的是数据库字段的特性，分别对应主键、无符号整数、非空、默认值等。但在上面的例子中，使用了子标签来定义这些数据库字段的特性。但我们也可以使用下面这种方式来定义 foo 这个数据库字段：  

```
                <field type="integer" notnull="yes">
                        <name>foo</name>
                        <default value="0" />
                </field>
```
<br>

因此，除了上面所说标签是由开发者自行定义的之外，使用 XML 来定义结构化数据时，我们可以自行定义数据的结构化层级关系以及元素属性。比如定义一个字段时，我们可以将名称、类型、主键、无符号、默认值等特性作为字段的子元素来定义，也可以使用标签属性来定义。当然，结构化数据的层级关系往往是比较清晰的，但就特定的元素特性来讲，哪些适合定义为元素属性，哪些适合定义为子元素，则没有统一的准则，大部分情况下由特定 XML 文件格式的开发者自行确定。  

比如上面给出的定义 foo 这个数据库字段的两种方式中，我们始终使用标签属性 type来定义数据库字段的类型，这是因为数据库字段的类型是固定的，而不是任意值，通过标签属性定义类型，可帮助我们使用 XML 的严格语法检查机制来查验文档的合法性。这涉及到 DTD（Document Type Definition，文档类型定义）的概念。  

### 6.4.2  DTD  

如果要使用 DTD 来验证一个 XML 文档的合法性，则需要在 XML 文档的头部增加 DOCTYPE 定义。本质上，DTD 定义了一个 XML 文档如下几个方面的规则：  

- 根元素名称。在我们的数据库表格示例中，根元素为 schema。
- 元素之间的嵌套规则，比如一个元素可以包含什么样的子元素、哪些子元素是可选的等等。以清单 6-2 为例，根元素 schema 中可包含一个或多个 table 元素，table 元素中要至少包含一个 field 元素，而 filed 元素中可包含多个可选子元素，如 notnull、key 等等。
- 有效的元素属性、属性默认值、属性的值类型（如字符串、枚举量等）。以清单 6-2 为例，filed 元素的属性有 name、type、size 等：name 属性是必须定义的，类型为字符串；type 属性是必须定义的，类型为枚举量；size 属性不是必须的，类型为字符串。  

DTD 的定义相对来讲比较复杂，比如就清单 6-3 所示的 XML 文档，对应的 DTD 大致如清单 6-4 所示。  

<small>清单 6-4  数据库表格的 XML 描述文档对应的文档类型描述</small>  

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE my_schema[
<!ELEMENT schema (table*)>
<!ELEMENT table (field+)>
<!ELEMENT field (key?, unsigned?, notnull?, default?)>
<!ELEMENT key EMPTY>
<!ELEMENT unsigned EMPTY>
<!ELEMENT notnull EMPTY>
<!ELEMENT default EMPTY>
<!ATTLIST schema version CDATA #REQUIRED>
<!ATTLIST table name CDATA #REQUIRED>
<!ATTLIST field
    name CDATA #REQUIRED
    type (I|C) #REQUIRED
    size CDATA #IMPLIED
>
<!ATTLIST default value CDATA #REQUIRED >
]>
<schema version="0.3">
...
</schema>
```  

清单 6-4 中，大写的单词是 DTD 的保留字，读者根据保留字的英文含义，大致就可以看明白 DTD 的定义方法。需要注意的是，清单 6-4 仅仅给出了针对清单 6-3 示例 XML 文档的最小文档类型定义；一个完整用于描述数据库表格格式的 XML 文档之 DTD，要比清单 6-4 复杂很多。清单 6-3 这个示例其实来自于 ADOdb XML Schema，其完整描述可见网页：[http://phplens.com/lens/adodb/docs-datadict.htm#xmlschema](http://phplens.com/lens/adodb/docs-datadict.htm#xmlschema)，对应的 DTD 见清单 6-5。  

<small>清单 6-5  ADOdb XML Schema 对应的 DTD</small>  

```
<?xml version="1.0"?>
<!DOCTYPE adodb_schema [
<!ELEMENT schema (table*, sql*)>
<!ATTLIST schema version CDATA #REQUIRED>
<!ELEMENT table ((field+|DROP), constraint*, descr?, index*, data*)>
<!ELEMENT field ((NOTNULL|KEY|PRIMARY)?, (AUTO|AUTOINCREMENT)?, (DEFAULT|DEFDATE|DEFTIMESTAMP)?, NOQUOTE, constraint, descr?)>
<!ELEMENT data (row+)>
<!ELEMENT row (f+)>
<!ELEMENT f (#CDATA)>
<!ELEMENT descr (#CDATA)>
<!ELEMENT NOTNULL EMPTY>
<!ELEMENT KEY EMPTY>
<!ELEMENT PRIMARY EMPTY>
<!ELEMENT AUTO EMPTY>
<!ELEMENT AUTOINCREMENT EMPTY>
<!ELEMENT DEFAULT EMPTY>
<!ELEMENT DEFDATE EMPTY>
<!ELEMENT DEFTIMESTAMP EMPTY>
<!ELEMENT NOQUOTE EMPTY>
<!ELEMENT DROP EMPTY>
<!ELEMENT constraint (#CDATA)>
<!ATTLIST table
    name CDATA #REQUIRED
    platform CDATA #IMPLIED
    version CDATA #IMPLIED
>
<!ATTLIST field
    name CDATA #REQUIRED
    type (C|C2|X|X2|B|D|T|L|I|F|N) #REQUIRED
    size CDATA #IMPLIED
>
<!ATTLIST data platform CDATA #IMPLIED>
<!ATTLIST f name CDATA #IMPLIED>
<!ATTLIST DEFAULT value CDATA #REQUIRED>
<!ELEMENT index ((col+|DROP), CLUSTERED?, BITMAP?, UNIQUE?, FULLTEXT?, HASH?, descr?)>
<!ELEMENT col (#CDATA)>
<!ELEMENT CLUSTERED EMPTY>
<!ELEMENT BITMAP EMPTY>
<!ELEMENT UNIQUE EMPTY>
<!ELEMENT FULLTEXT EMPTY>
<!ELEMENT HASH EMPTY>
<!ATTLIST index
    name CDATA #REQUIRED
    platform CDATA #IMPLIED
>
<!ELEMENT sql (query+, descr?)>
<!ELEMENT query (#CDATA)>
<!ATTLIST sql
    name CDATA #IMPLIED
    platform CDATA #IMPLIED,
    key CDATA,
    prefixmethod (AUTO|MANUAL|NONE)
>
] >
```
<br>

另外，我们也可以在 XML 文档中引用单独存放的 DTD 文件，而无需在 XML 文档中包含完整的 DTD 定义。比如针对上面的示例，我们可以将清单 6-4 中的 DTD 保存为 adodb\_xml\_schema.dtd 文件，并保存在互联网上，然后使用下面的方式访问这个 DTD 文件：  

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE schema SYSTEM "http://www.mydomain.com/dtds/adodb_xml_schema.dtd">
```
<br>  

限于篇幅，本书不打算详细讲述 DTD 的细节，有兴趣的读者可自行参阅相关书籍或技术文档。  

### 6.4.3  XML 的应用  

由于 XML 可用来定义复杂的结构化数据，且具有较好的人机共读特性，因此，XML 得到了广泛应用。比如上面的示例使用 XML 文档描述数据库结构，而在 Android 系统中，XML 被用来描述图形用户界面，也被用来定义本地化字符串。但使用 XML 最多的场合则是 Web 服务。所谓 Web 服务，也是通过 HTTP 协议提供的，只是其返回的内容格式不是通常的 HTML，而是 XML；HTML 给浏览器使用，最终为普通用户渲染出图文并茂的网页，XML 则是提供给另外一个计算机程序使用的，由这个程序做进一步的处理。也就是说，我们可以这样理解 XML 的出现：XML 是为了将服务器可以提供的数据通过万维网传输给其他计算机（或程序）使用而发明的。知道了这一点，我们也就很容易理解为什么 XML 的语法要比 HTML 严格很多，当然也更加灵活。  

因此，在 Web 服务大行其道的今天，大部分 Web 服务提供的结果是通过 XML 来描述的。然而，随着 JSON 的流行，一些新开发出来的 Web 服务，不再使用 XML 来描述结果，而使用JSON 描述结果。  

## 6.5  JSON  

和 XML 类似，JSON（JavaScript Object Notation，JavaScript 对象表示法）是一种轻量级的数据交换格式。但 JSON 明显比 XML 具有更好的特性：易于人阅读和编写（JSON 不使用尖括号和标签），同时也易于计算机解析和生成。从 JSON 的名称可知，这种结构化数据的表示法来源于 JavaScript 编程语言，也就是 Web 应用的唯一编程语言，这个编程语言以易学易用著称。  

JSON 采用完全独立于语言的文本格式，但是也使用了类似于 C 语言家族（包括C、C++、C\#、Java、JavaScript、Perl、Python 等）的习惯。这些特性使得 JSON 成为理想的数据交换格式。JSON 的构造基于如下两种基本形式：  

- 名称/值对的集合。在不同的编程语言中，“名称/值对”被理解为对象、记录、结构、字典、哈希表、键值对或者关联数组。
- 有序的值列表。即大部分编程语言所指的数组。  

这些都是各种计算机编程语言中常见的数据结构。事实上大部分现代计算机编程语言都以某种形式支持这些数据结构。因此，JSON 描述的数据在同样基于这些结构的编程语言之间交换时非常方便。  

清单 6-6 给出了一个用 JSON 描述结构化数据的示例。这个示例用 JSON 描述了清单 6-3 中使用 XML 描述的数据库表 foo\_bar 的结构。  

<small>清单 6-6  用 JSON 描述数据库表的结构</small>  

```
{
    name: "foo_bar",
    fields: [
        {name: "id", type: "I", key: "yes", unsigned: "yes"},
        {name: "foo", type: "I", notnull: "yes", default: 0},
        {name: "bar", type: "C", size: 32, notnull: "yes", default: ""}
    ]
}
```  
<br>

对比清单 6-3 和清单 6-6，我们可以看到使用 JSON 描述的数据库表格的结构更加清晰。一个 JSON 对应于一个对象，一个对象可以有多个属性，每个属性有其名称和值。而属性的值可以是字符串、数值或者数组，也可以是另外一个对象，除此之外，还可以有 true（真）、false（假）、null（空） 等三个特殊值。比如我们定义 foo\_bar 这个数据库表格时，对应的 JSON 对象中包含两个属性，用花括号（{}）包围：一个是 name，用来表示表格的名称，其值为字符串“foo\_bar”；一个是 fields，用来定义表格的字段，其值为数组，用中括号（[]）包围。  

fields 包含三个字段，每个字段又是一个 JSON 对象，每个对象用花括号（{}）包围，中间用逗号（,）分隔。这些描述字段的对象有 name、type、key、unsigned、notnull、default 等属性，分别使用字符串、数值等作为相应属性的值。  

需要注意的是，清单 6-6 在定义不同的字段时，这些字段对象的属性并不完全一样。在 JavaScript 中，未定义的属性对应的值是 undefined，这个值和空值（NULL）不同，而在其他编程语言中，可能无法区分这两种情况。因此，程序在解析上述 JSON 数据时，需要注意这个区别。当然，我们也可以强制所有的字段对象都具有相同的属性，并对这些额外的属性取默认值，如清单 6-7 所示。  

<small>清单 6-7  用 JSON 描述数据库表的结构（增强版）</small>  

```
{
    name: "foo_bar",
    fields: [
        {name: "id",  type: "I", size: null, key: true,  unsigned: true,
             notnull: true, default: 0},
        {name: "foo", type: "I", size: null, key: false, unsigned: false,
             notnull: true, default: 0},
        {name: "bar", type: "C", size: 32,   key: false, unsigned: null,
             notnull: true, default: ""}
    ]
}
```  

清单 6-8 给出了一个真实的 Web 服务接口返回的 JSON 数据示例。这个 Web 服务接口返回指定国家的编码、英文名称、标准简写以及已知区域的本地化名称。需要特别说明的是 localizaed\_name 对应的字符串值使用了 \u 作为前缀，这指 \u 之后定义了一个 UNICODE 字符，用四个十六进制字符表示，是 UNICODE 字符的内码值。  

<small>清单 6-8  用 JSON 描述阿富汗的国家码以及不同区域下的名称</small>  

```
{
    "status": "ok",
    "endpoint": "/list/countries/item_detail/{token}/{numeric_code}",
    "items": {
        "numeric_code": "4",
        "name": "Afghanistan",
        "alpha_2_code": "AF",
        "alpha_3_code": "AFG"
    },
    "extras": [
        {
            "locale": "en",
            "localized_name": "Afghanistan"
        },
        {
            "locale": "zh",
            "localized_name": "\u963f\u5bcc\u6c57"
        },
        {
            "locale": "zh_CN",
            "localized_name": "\u963f\u5bcc\u6c57"
        },
        {
            "locale": "zh_HK",
            "localized_name": "\u963f\u5bcc\u6c57"
        },
        {
            "locale": "zh_TW",
            "localized_name": "\u963f\u5bcc\u6c57"
        }
    ],
    "elapsed_time": "0.0088",
    "memory_usag": "0.65MB"
}
```  
<br>

如前所述，由于使用 JSON 描述结构化数据时简洁的表达方式、便于各种各样系统的处理、便于人机共读的这些特点，JSON 已有取代 XML 的趋势，很多新系统已经开始仅提供 JSON 格式的数据而不再提供 XML 格式的数据了。  

<br>  

[^1]\: 有关文字输出的双向排版问题，将在本书第五篇“信息的计算机展现”中讲述。  

[^2]: 亦定义有针对不同处理类型的环境变量 LC\_COLLATE（字符串排序规则，用于正则表达式）、LC\_CTYPE（字符类型，用于正则表达式）、LC\_MESSAGES（本地化字符串）、LC\_MONETARY（用于货币格式化）、LC\_NUMERIC（用于数字格式化）、LC\_TIME（用于时间和日期的格式化）。  

[^3]: 亦简写为 i18n，其中的 18 表示 I 和 n 之间有 18 个字母。  

[^4]: UTC 是 Universal Time Coordinated（协调世界时）的简写，即格林威治时间。  

[^5]: 需要注意的是，从操作系统软件栈功能的划分角度讲，时区并不是由操作系统内核维护的，而是由标准函数库维护的。由于 gettimeofday/settimeofday 这两个接口涉及到了时区的获取和设置功能，因此在后来的标准修订中不再推荐使用这两个接口，转而推荐使用 clock\_gettime 等接口。  

[^6]: 感兴趣的读者可查阅 W3C 官方网站：http://www.w3.org。  

[^7]: 在这个小节，请注意特性（Property）和属性（Attribute）这两个术语的使用场合。前者泛指元素或对象的性质、特性，如人员年龄、性别，后者特指 XML 的标签属性。  
