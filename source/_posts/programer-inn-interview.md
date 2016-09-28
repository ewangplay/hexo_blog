title: 一篇采访稿
date: 2016-09-12 18:02:11
tags: 采访稿
---
上周五程序员客栈的王鑫找到我，说想以程序员和项目经理的身份让我接受一下采访，然后在全渠道推广一下，这让我有点受宠若惊，因为从小到大，采访这种高端节目跟我这种普通地掉渣的人向来无缘，基本只能在电视媒体上目睹这样的高端节目，一旦好运降临到自己身上，自然是有点浑身不适应啊。

但不适应归不适应，还是要硬着头皮上，因为这是好事啊。不只对我自己，对于程序员客栈，对于其他像我一样的程序员，甚至对于普通读者，都没啥坏的影响，因为我说的都是实话，只要是真言，都值得鼓励，何况像我这种名不见经传的小人物，用平实的语言回顾一下自己的十年人生，即激励了自己，也娱乐了大众，何乐而不为呢？

所以，下面就是我的未删减版采访稿，想看官方标准版，请移步[这里](https://www.proginn.com/community/topics/356)。


## 您是否可以简单的做一下自我介绍？
大家好，我是王晓辉，毕业后当过两年的高中老师，于2006年辞职到北京从事专业程序开发工作，至今已10年，先后在私企、外企、互联网公司、创业公司里做程序开发和技术管理工作。做过的项目包括个人PC安全软件、自然语言处理（KNLP）、移动电信联通网关业务、数字证书认证系统、安全数据代理软件、自动化营销SaaS系统，在Windows和Linux下均积累了丰富的开发和项目管理经验。

## 您是什么大学毕业的？你是如何接触到编程开发的？
我毕业于信阳师范学院计算机科学与技术专业，信阳师范学院只属于二本院校，名气不大，但学风很好，大学四年为我打下了扎实的计算机理论功底，再加上当时跟同学经常一起泡在学校外面的电脑培训班（学校里面的上机课严重不够啊）里学习各种编程技巧，从Basic到Pascal，再到C、Visual Basic、Visual FoxPro，到最后的C++/MFC，都自学自画的捯饬过。现在想想还是很还念那时候的时光啊。

## 您毕业后都在哪些公司工作过，学到了什么？
我2006年辞去老家高中老师的工作，只身来到北京，开始第一份纯技术开发工作，这是一家私企——北京卡斯特信息技术有限公司——既有自己的产品（个人PC安全软件），又做外包业务。我在公司里做了大半年自己公司的产品后，就被外派到日企佳能（Canon）公司，开始做一个韩文自然语言处理（KNLP）的项目，这一做就是两年多。在这里，我首次经历了一个完整的项目周期——需求分析、概要设计、详细设计、编码、单元测试、系统测试、验收测试。我的C/C++/MFC技能在那里得到了一次质的提升，虽然之前看了很多的书和做了很多练习，但都跟实际的项目经验差别甚大。核心的底层模块都是用C语言完成的，而且要求跨平台运行，所以在这里也首次开始在Linux系统下做开发，首次使用VIM+GCC+GDB的组合方式来开发调试程序。上层的UI是用C++/MFC/ATL完成的，以库的方式调用底层核心的模块。这个项目整整做了两年，在当时的我看来，这是一个很大的项目，用到了C/C++/MFC/ATL等编程技术，使用了模块化的设计，采用了典型的瀑布开发模式，从需求、设计、编码、单元测试、验收测试，到最终的产品发布，都给了我全新的体验。

KNLP项目完成后，我回到原公司，公司的情况已经不容乐观，本身的产品没有很好的市场，外包业务也在萎缩，再加上公司上层的组织架构发生了很大的变化，导致很多老同事纷纷离开。我在公司只呆了半年，零零星星的又做了一些小项目，也选择了离开。

后来就去了亿阳信通。亿阳信通有自己的软件园，有自己的办公大楼，有自己的餐厅，有自己的班车，算是一家很大的公司。

我当时去的部门，是做电信、移动、联通等网关业务数据分析，就是把硬件设备采集到的业务数据进行分析、存储、汇总、页面展现等。底层模块中很大一部分都是做数据分析处理的，但看了之后才发现，如此庞大的一个数据处理系统，全部构架在Perl语言之上的，可能当时架构设计者认为Perl是动态语言，易于开发，易于维护，易于扩展。这个想法原本也是不错的，但任何系统，当达到一定的规模后，易于扩展和易于维护都变得不再容易。我到现在仍然认为Perl语言并不是很适合构建大规模的软件系统，特别是在处理性能上存在瓶颈。在亿阳信通的两年里，除了后来为了特性性能做的一些C/C++分析模块，大部分都还是跟Perl语言打交道。

后来公司领导也觉得原先的数据处理系统在性能上差强人意，就让我们组针对特殊项目开发独立的数据处理模块，使用C/C++语言来完成。当时开发的数据解析程序是运行在Solaris系统上，我先在自己的Ubuntu系统上开发完成，然后再移植到Solaris系统上调试运行。因为Solaris系统跟我们平时使用的计算机CPU架构是不一样的，我们通常使用的x86架构的计算机，硬件编码的方式是小端（Little-Endian），而Solaris系统是大端（Big-Endian）。这也是当时开发过程中特别注意的一个方面，所以至今记忆深刻。

在亿阳信通的两年里，我开始接触开源项目，自己也尝试写一些开源的程序，比如Linux下GNOME/GTK图形库，就是在写开源程序的过程中掌握的，实际工作中并没有用到。当时还没有github，还是google code的代码仓库。因为当时我醉心于Linux/Ubuntu, 所以开发的开源软件都是运行在Linux/Ubuntu之上的。

后来，公司要跟IBM进行战略合作，但雷声大雨点小。我们项目组按照部署要跟IBM进行合作，对他们的业务质量管理产品做二次开发，满足国内业务的需求。但合作进展缓慢，有一段时间几乎无所事事，浪费青春啊，是时候要离开了。

我接着加盟的公司叫吉大正元信息技术股份有限公司，总部在长春，我入职的是它的北京研究院。当时吉大正元正准备开发一款数字证书身份认证与数据代理网关的产品，刚好在搜罗人才，而我也刚好从亿阳信通离职，很快就受邀加盟了吉大正元的北京研究院。我入职的第二个星期，整个研发团队就被拉到怀柔雁栖湖畔山脚下的一个小楼开始了封闭开发，没想到一呆就是五个多月。提起封闭开发，可能很多人会觉得很苦很枯燥，但后来跟一起封闭开发过的兄弟谈起，都很怀念那段时光。封闭开发相当于给程序员们提供了一个理想的编程环境，尽量排除外界因素的打扰，全身心地专注在产品研发上。这对于程序员的职业成长来说，实际上是一件幸事啊。 

产品主要是使用C、C++和Java完成——服务端核心代理模块使用C完成，后台管理和登录会话管理模块使用Java完成，客户端使用C++/MFC/ATL完成。当时公司决定在这次封闭开发中采用敏捷开发模式，还专门从外面请了敏捷教练进行全称培训，这也是我首次接触敏捷开发理念和敏捷实践，像迭代开发、故事墙、每日站立早会、结对编程，代码集体所有权、自动化构建，自动化部署，自动化测试等等，都为我们打开了一个全新的视野，让我看到跟之前传统的瀑布开发模式完全不一样的开发流程。所以说，在封闭的这半年里，是我职业生涯中又一个快速提升的关键期，可以跟在佳能研发中心的那两年相媲美。

封闭开发结束后，网关产品的核心功能都已完成，之后就是对产品不断完善和修复bug的过程。在这一个过程的开始，就已经把产品推向了市场，然后不断的完善功能，修复Bug，以快速迭代的方式发布新版本。这就是敏捷中快速发布，而不像传统的软件发布那样等一切就绪了再发布产品。传统的软件发布反应迟缓，错失机会。而敏捷的发布模式却可以在核心功能和最小需求完成的情况下，快速推向市场，让市场和用户来检验产品，提出新需求，发现问题，从而快速有效的完善产品，持续不断的发布新版本。

在吉大正元北京研究院的三年零五个月的职业生涯中，我又一次收获了很多。我接触和学习了敏捷开发理念，掌握和实践了敏捷开发活动，分析和掌握了很多的网络协议，对网络编程的理解和实践达到了一个新的高度，对Linux下的大并发多线程高性能的服务器开发有了深刻的理解和项目积累，对Linux的防火墙iptables的使用开始得心应手，对一些开源软件——apache, openssl, openvpn的借鉴让我的设计能力得到了很大的提升。是吉大正元又一次把我推上了一个新的高度，我很感谢她。

后来，产品推向市场后，经过多次的迭代开发，趋于完善和稳定。而我又一次选择了离开，我就像永远走在路上的朝拜者一样，只在某处逗留几天，但终会再一次出发，走向远方。

这一次，我决定再给自己一个新的挑战——进入纯粹的互联网领域，加入纯正基因的互联网公司。我离开吉大正元的时候，北京九枝兰信息技术有限公司刚成立三个月，做互联网的自动化、一站式营销平台，刚好当时的CTO找到了我，邀请我加入。CTO是个很有情怀的人，跟他谈的很投机，可谓一拍即合，于是就选择了加入九枝兰。

我时常对自己说：只要用心去追求，总会收获不一样的风景。在生活中如此，在职业生涯中也是如此。在九枝兰这两年里，我完成了我职业生涯的三级跳——第一跳是在佳能研究院做KNLP项目时，让我经历了完整的瀑布开发模型；第二跳是在吉大正元北京研究院做G3000产品时，让我经受了敏捷开发模式的洗礼；第三跳就是在九枝兰，让我融入了互联网的基因，全程设计和开发了基于微服务架构的云服务平台。

在这两年里，我首次使用并熟练掌握了新的编程语言——Go，之前的产品和项目主要都是基于C和C++进行开发，而Go语言作为互联网时代的C语言进入了我的视野，而且让我欲罢不能，这两年开发的大部分代码都是使用Go完成的。我首次在大规模云服务平台上使用微服务(Micorservices)的架构模型，紧跟了互联网时代的设计思想。我们的团队也在云服务中引入Docker的实例管理，让云服务的构建和部署更加的轻巧和灵活。还有公司扁平化和自组织的管理模式，也让开发管理变得更为简单高效。所有这些，都是九枝兰带给我的丰收硕果。

以上就是我差不多十年工作的一个经历和总结，现在回顾那段激情燃烧的岁月，还真是感慨颇多啊。

## 你都参与过哪些知名项目？
1. MarketingOS一站式营销系统—— 集成了搜索引擎投放、搜索引擎优化、微信、微博、H5模板、着陆页、邮件、短信、DSP、广点通、APP 消息推送等多个推广渠道，结合用户管理、营销活动管理、营销自动化、渠道分析、用户分析、数据分析等功能，帮助企业主提高效率，节省营销成本。 项目使用的主要编程语言有： Golang、Python、Java、PHP、Javascript。项目使用的关键技术有：RESTful API、Thrift、Apache Kafka、NSQ、Docker、Mysql、MEAN等。
2. 身份认证与数据代理网关G3000—— 吉大正元自主研发的基于数字证书身份认证和安全数据代理的内网安全产品，主要针对国内对内网安全要求比较高，需要进行身份鉴别和权限控制，而且对数据在传输过程的安全有极高要求的企事业单位。 项目使用的编程语言：C、C++、Java。项目使用的关键技术：TCP/IP/SSL/HTTP、Sock5、Apache、Openssl、Openvpn、Netfilter/Iptables、TUN/TAP、MFC/ATL/STL、Windows NDIS驱动等。
3. 自然语言合成系统KNLP—— 该项目是佳能自主研发的一个韩文自然语言处理系统，要对韩文的语言文字做语音合成处理，最终要用于佳能的硬件设备上，如相机、复印机等。 项目使用的编程语言：C、C++。当时做项目还很少使用开源库，所有的功能模块全都是用代码纯手工打造出来的，所谓的软件匠艺，在早期的项目中反而有更好的体现啊，哈哈。

## 是什么让你励志成为一名程序员？

说实话在上大学之前很少摸到计算机（那是1999年之前，计算机对普通人还是比较神秘的事物），也就是玩过小霸王游戏机什么的，还可以搞一搞BASIC编程。后来考大学报专业的时候就觉得计算机这个东西这么酷，将来肯定有前途，就选择了这个专业，从此就走上了程序员这条不归路啊。。。


## 对你来说一名项目经理需要具备什么样的硬实力？

作为一个项目经理，我觉得首先要了解技术，不要求对技术多么精通，那是程序员干的事情，但作为一个项目管理者，如果对自身项目使用的技术领域不了解的话，可能就会两眼摸黑、盲人摸象，在管理过程中沟通的效果就会大打折扣。

其次就是要对项目的风险因素有一个清晰的把控。项目的时间安排上是不是充足？人员安排上是不是到位？关键技术上是不是有瓶颈？团队成员的工作状态是不是很好？把这些因素一一的考虑到，如果有风险要及时的进行调整。比如项目实际的进度慢了，是什么原因造成的？是开始的时候时间估算就不准确，还是因为遇到技术瓶颈进展缓慢，还是因为团队成员士气不振出活不多？根据实际的情况分析出原因找到解决的办法。

其次就是团结的能力。项目经理就是项目的粘合剂，把所有的项目成员粘结在一起，把一个共同的目标完成。这个过程中，团队成员之间可能会出现各种各样的沟通问题，甚至跟你这个项目经理之间也会产生分歧，这时候怎么办？就需要项目经理放低姿态，不要自认为是一个管理者，而是一个兄长、一个朋友的身份，设身处地的为对方考虑，沟通到位，把问题说开辨明。大家最终的目标都是一致的，在路途中某些人稍微偏离了一点，就要及时的把他们拉回大家一起的道路上，共同向目标进发。

## 外包公司和大的互联网公司工作的区别在哪里？

外包公司和做自己产品的互联网公司我都经历过，所以我就谈一下自己的感受吧。

作为一个程序员的职业成长来说，并不能说在什么样类型的公司发展孰好孰坏，没有一个绝对的标准。关键还是看你自己在这个过程中是不是得到了成长，学到了有价值的技能。而这一点在两种类型的公司中都可以做到。

就比如我在佳能做KNLP项目时，就属于外包性质的，但那两年却是我职业发展提升很快的一段时期，让我至今受益匪浅。再比如我在吉大正元和九枝兰，都是做自己公司的产品，同样也是成长最快的一段职业发展时期。

但总体上，在互联网公司做自己的产品还是要强过做外包项目，主要的原因是做自己的产品，你基本上可以更深入、更持久、更全面的进入这个项目，自然而然学到的东西就会更多一些。而外包项目要看项目，如果你碰到一个好的项目，就像我在佳能参与的KNLP项目，而且外包方对你非常信任，让你接触核心的技术领域，那也是很不错的。

## 就以你个人而言，比较擅长哪方面项目开发？你都有哪些优势？

就我个人而言，我比较擅长云服务、数据安全和代理、微信公众号平台、企业微服务等类型的项目开发，在这些方面都有过实战经验。我个人的优势在于做过多年的安全产品，对数据安全和网络协议栈非常了解，这两年又专门做基于微服务的云服务平台，所以对后端服务的技术架构也非常了解。因为做过的项目类型比较多，所以在项目的实施过程中就可以参考借鉴别的项目中的长处，好的技术架构，好的管理模式。

## 你平时除了还有哪些兴趣爱好？

我除了本职的技术开发和技术管理工作外，平时还喜欢跑步、游泳、骑行、爬山和读书，现在基本上要求自己每周跑两次步、游一次泳，至于读书每年会选择一些优秀的书籍读上10本左右。

## 你会一辈子做程序员吗？你个人的职业生涯是如何规划的？

我记得当年初次北上来到我大北京的时候给自己的人生设计过三个目标：第一个是成为一个优秀的程序员，我用了十年时间，可以说已经达到了目标；第二个是成为一个独立的咨询师、技术顾问和技术作者，这是我接下来要努力的目标；第三个是还回到校园教书，把我一生所学再教给我的学生们。这样我一生足矣！

## 作为一名经验丰富的程序员，请问遇到过哪些巨大的挑战？

经历过这么多的项目开发，总会遇到这样那样的困难和挑战，现在想来很多都不是什么大不了的事情了，但在当时还是需要咬咬牙关挺一挺的。

我记得在吉大正元做G3000产品封闭开发期间，封闭了5个多月，前期按部就班还算正常，但到了后半段，因为封闭时间太长，有些同事情绪上就出现了一些问题。有一个小伙伴刚好因为家里也出现了一些状况，就突然提出了辞职，要求退出封闭开发，他是客户端开发团队的成员，而当时客户端开发正处于紧张的时期，人手本来就不富足。这样一来肯定会影响整个开发进度，后来就决定征调一名服务端开发团队的人来顶上。我当时就属于服务端开发团队的，而服务端开发进度正常，本来可以轻轻松松地完成封闭开发任务。我跟另一个哥们就被征询意见：看谁同意调到客户端来？那哥们是死活不同意，说是对客户端开发不熟悉。好吧，那我就上吧。反正记得刚接手客户端那阵搞得晕头转向啊，已经开发了很多代码，首先要先熟悉这些代码，才能继续在上面做开发，在加上本来任务就重，就只能加班加点的赶啊。反正最终呢，还算是皆大欢喜，封闭开发的任务基本都完成了，公司后来对封闭开发的人员都做了奖励，也算是我们的辛苦有了回报。

其它项目中遇到的困难和挑战不一而足，总体上属于临危受命性质的，反正困难来了，你硬着头皮，咬紧牙关顶住，总会柳暗花明的。而这种时候也正是你成长最快的时候，正所谓百炼钢才能化为绕指柔，不经过千锤百炼，怎么能够成为无坚不摧的利器呢。

## 做程序员时间久了，有哪些好的建议给刚入行的开发者？

对于刚入行的开发者，我给出以下几点建议：
1.	做好自己的职业规划。你对自己的人生还是要有一个整体的认识和规划，这几年你想做什么？你希望自己经过这几年的发展达到什么样的高度？5年以后、10年以后你希望自己做什么？为了达到这些目标，我应该怎么去做？这些问题虽然不容易回答，但属于你人生层面的大事，一下子想不明白，你可以暂时把它放入后台，想起来了调入前台思考思考，总会有所收获的。
2.	工作选择以自己的成长为出发点。一个工作的好坏，因人而异。但对于我来说有一个简单的标准：这个工作在我为企业公司做出贡献的同时，能不能让我个人得到成长？这是一个选择公司、选择工作的很关键的标准。如果一个工作岗位福利很好，待遇从优，但是你在这个岗位上干十年，个人能力上跟你干一年没啥区别，这种工作起码对于我来说没有任何吸引力啊。
3.	开放的心态和学习能力。选择程序员这个职业就是一条不归路——活到老学到老啊，所以在这个行业里，就要有开放的心态和快速学习能力。平时多看书，项目中多实践，多跟前辈程序员们请教学习，一点一点积累技术能力，一天一天的成长，这样的人生才是快乐而充实的啊。
4.	人脉是你人生的财富啊。在每一个公司，你都会融入一个团队，结交一些志同道合的朋友，你们在一起敲代码，在一起开玩笑，在一起加班累成狗，在一起谈天说地。即使有一天你离开了这个团队，你生命中的这段经历永远不会抹去。有事没事了找他们聊聊，或者有机会了约个面见见，“有朋自远方来，不亦乐乎”，真乃人生一件幸事啊。说不定将来你的那个整天跟你调侃的程序猿邻桌就成了什么公司的C什么O、什么总啊，你们有了业务上来往，那不是人生财富是什么呢。

## 你如何看待未来共享经济环境下程序员自由工作的前景？
共享经济这个概念已经炒的很火了，现在有点见识的人都张口闭口“共享经济”，不然不能称之为紧跟时代潮流啊。那么到底什么是共享经济，以及共享经济对未来程序员职业发展有什么巨大影响呢？如果用一句话来说明一下什么是共享经济，那我觉得是：共享经济就是将闲置的资源共享给别人，提高资源的利用率，并让共享资源的人从中获得回报。共享经济的本质就是互助+互利。
未来的工作模式将是由独立的个体相互自由连接而成的一个既松散又紧密相连的网络。正如凯文-凯利在他的《失控》中所阐述的思想：有机的活系统，依赖于分布式的自管理的子系统，而各个子系统又依赖于分布式的自管理的个体，没有中枢，没有一个统一的自上而下的管理，每个个体相互联系，相互反馈，相互协作，创造了一个生机勃勃、充满活力的有机活系统。看似杂乱，却有条不紊地运转。
而作为共享经济下时代的弄潮儿——程序员，有着自身独特的价值优势。首先程序员的知识技能非常有利于在互联网环境下分享；其次程序员这种群体的气质非常独特——追求自由独立而又相依相存，恰好符合共享经济的特质。

再有就是互联网的发展阶段已经为共享经济的繁荣提供了基础设施。共享经济并不是什么独创性的概念，也很容易理解，但为什么前二十年、前十年没有喊得这么响，没有这个突飞猛进？那是因为原先的个体互联互通的基础设施——互联网还不够完善，还不足以支撑起共享经济这个平台的搭建。而现在互联网的发展已经渗透到社会发展的方方面面，任何人不接入互联网就相当于跟时代隔绝了，所以共享经济的基础设施已经完备。

应运而生的，各种共享经济平台如雨后春笋般纷纷出现，程序员客栈正是这里面的优秀代表——针对程序员的共享经济平台。程序员不再局限于自己的公司、自己所处的区域，在任何时间、任何地点，只要你有了闲置的时间，又愿意把自己的技能分享出去服务于别人，就可以接入这个共享经济平台，获得回报。同时你的视野将放眼全球，你所做的项目可能在异国他乡，你一起的工作的队友可能在千里之外，你们之间通过这绵绵不绝的网络相互连接，同时连接这个世界。

这就是未来工作的前景，我们期待着美好生活的到来。