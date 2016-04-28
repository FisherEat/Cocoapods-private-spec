Xcode project中引入Cocoapods管理

项目组件化、平台化是技术公司的共同目标，越来越多的技术公司推崇使用pod管理第三方库以及私有组件，一方面使项目架构更加清晰，一方面现有的工具Cocoapods提供了近乎完美的解决方案。这里我们来讨论一下如何在Xcode 工程中集成Cocoapods，这里提供入门级别的集成和进阶集成方式。

本文目录

参考文档汇总

1. Cocoapods官网:https://cocoapods.org/
2. Cocoapods在github上的开发仓库：https://github.com/CocoaPods/CocoaPods
3. Cocoapods 错误汇总 https://github.com/CocoaPods/CocoaPods/issues
4. Cocoapods帮助界面http://guides.cocoapods.org/using/using-cocoapods.html
5. Cocoapods入门参考文档：http://www.tuicool.com/articles/RzI3ye
6. 创建私有pod参考文档： http://www.cocoachina.com/ios/20150228/11206.html
7. 私有Pods: http://blog.wtlucky.com/blog/2015/02/26/create-private-podspec/
8. 淘宝技术架构：http://blog.cnbluebox.com/blog/2014/03/31/cocoapodsdai-ma-guan-li/

项目中配置Cocoapods解决方案

Cocoapods简介

Cocoapods是OS X和iOS下的一个第三方库管理工具，并且支持Objective-C和swifit语言。通过Cocoapods为项目添加称为"Pods"的依赖库（这些库必须是Cocoapods所支持的），并且轻松实现第三方库的版本管理。

引入Cocoapods的意义：

Cocoapods在引入第三方库时自动为我们完成各种配置，包括配置编译阶段、连接器选项、甚至是ARC环境下的-fno-objc-arc配置等

Cocoapods可以很方便的查找第三方库，可以快速方便的寻找到优秀的第三方库以及它们的各种信息。

Cocoapods 官方网站：

https://cocoapods.org/

Cocoapods整个项目托管在github上， 所有的Pods依赖库也都依赖github

https://github.com/CocoaPods

Cocoapods发展背景

Cocoapods创建于2011年，目前Cocoapods团队有17位核心开发人员以及多达5000多个开源项目。Cocoapods的存在为代码分发提供了非常便捷的解决方案。

Cocoapods核心组件

Cocoapods是用Ruby写的，所以在安装时有时要更新ruby源。Cocoapods在解析过程中最重要的几个包的路径分别是：CocoaPods/CocoaPods、 CocoaPods/Core和 CocoaPods/Xcodeproj，其中Core提供了CocoaPods相关文件，主要是podfile和podspecs。

podfile：该文件用于配置项目所需的第三方库，可以被高度订制。

podspec：该文件描述一个库如何被加入到工程文件中。.podspec文件可以标识该第三方库所需要的源码文件、依赖库、编译选项，以及其他的第三方库所需的配置。

over.

Cocoapods与项目sdk化

有些项目处于某些模块采取的是将该业务代码打成framework，如比较重要的金融支付模块。我们在集成私有Pod过程中，将AFNetworking这种第三方库用用Cocoapods管理，其实是打成了.a，而金融sdk仍然使用framework，打成.framework，因此二者无法链接，在Link阶段报错。解决方案是，要么将金融打成.a，要么在金融打成sdk时不勾选AFNetworking。

gitmodule的关系与解决方案

子模块和Cocoapods都是试图解决同样的问题。使用子模块的业务准备迁移到Cocoapods 首先需要解耦，模块间解耦。

安装和使用Cocoapods

CocoaPods安装步骤

- 升级Ruby环境**

终端输入：

    $sudo gem update --system

- 安装CocoaPods要访问cocoapods.org,该网站已经被天朝墙壁，需要用淘宝镜像来安装 

    1、gem sources --removehttps://rubygems.org/

    2、gem sources -ahttp://ruby.taobao.org/

    3、gem sources -l 

- 安装Cocoapods

    sudo gem install cocoapods

Pod 常用命令

Pod创建Podfile文件有一下几个步骤：

- pod 常用命令

    搜索类库
    pod search AFNetworking
    更新第三方库： pod update导入第三方库：
    pod install 可以删除podfile中某个库的代码，然后： pod install 
    删除第三方库 ：pod uninstall 
    查看Cocoapods版本： pod --version
    查找第三方库： pod search AFNetworking

- $ cd 到工程文件目录，然后创建Podfile文件

    $ touch Podfile（创建Podfile文件）

- $ vim Podfile编辑该文件，然后通过wq保存（具体可参考vim命令行）

    platform :ios, '7.0'
    inhibit_all_warnings!
    pod 'SDWebImage', '3.7.3'
    pod 'SSZipArchive', '1.0.1'
    pod 'JSPatch', '0.1.5'
    pod 'TMCache', '2.1.0'
    pod 'FLEX', '2.2.0'

- 以后打开后缀名为 .xcworkspace文件打开工程

pod语义化版本规范

    pod 'AFNetworking'  // 不显式指定依赖库版本，表示每次都获取最新的版本
    pod 'AFNetworking', '2.0'  //只使用2.0版本
    pod 'AFNetworking', '> 2.0'     //使用高于2.0的版本  
    pod 'AFNetworking', '>= 2.0'     //使用大于或等于2.0的版本  
    pod 'AFNetworking', '< 2.0'     //使用小于2.0的版本  
    pod 'AFNetworking', '<= 2.0'     //使用小于或等于2.0的版本  
    pod 'AFNetworking', '~> 0.1.2'     //使用大于等于0.1.2但小于0.2的版本  
    pod 'AFNetworking', '~>0.1'     //使用大于等于0.1但小于1.0的版本 
    pod 'AFNetworking', '~>0'     //高于0的版本，写这个限制和什么都不写是一个效果，都表示使用最新版本

over.

主App link Pods库

在集成Cocoapods后，主App可以编译通过，但是无法link到Pods 库的情况。首先，我们看一下主App是如何引入Pods中的项目，编译没有问题，关键在于link。

安装和更新Cocoapods

更新Cocoapods有一下几个步骤：

- 首先更新gem 源，国内需要切换源
-     $ sudo gem update --system

gem安装位置：

/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/gem

    - 更换gem为淘宝镜像，国内需要切换源
    $gem sources --remove https://rubygems.org/
    $gem sources --add https://ruby.taobao.org/ 

注意：这是淘宝ruby镜像官网，已经换成了https协议https://ruby.taobao.org/

- 查看当前ruby源版本

    $gem sources -l

- 安装cocoapods

    $sudo gem install cocoapods

- 启动Pod

    $pod setup

- 

    $ sudo gem update --system

Podfile文件配置

指定平台和环境

    platform :ios, '7.0'
    inhibit_all_warnings!
    target 'MyPro App' do
    pod 'AFNetworking' , '~> 2.7.0'
    end

配置成功后执行$ pod install会显示

    Please close any current Xcode sessions and use `MyPro.xcworkspace` for this project from now on.

项目新增四个文件， Podfile、Podfile.lock、.xcworkspace、podfilelock.manifest以后就可以使用Pod来管理自己的组件库，

over.

Podfile与多target

Podfile本质上是用来描述Xcode工程中的targets用的。如果我们不显式指定Podfile对应的target，Cocoapods会创建一个名为default的隐式target，会和我们工程中的第一个target对应。换句话说，如果在Podfile中没有指定target，那么只有工程中的第一个target能够使用Podfile中的描述的Pods依赖库。根据需要，给不同的target指定Pods依赖库。

- 例如，名称为MyApp的target和MyPro app的target都需要Reachabilitiy、SBJson、AFNetworking三个Pods依赖库，可以使用link_with关键字来实现，将Podfile写成如下形式：

    link_with 'MyApp', 'MyApp pro'
    platform :ios 
    pod 'Reachability', '~> 3.0.0'
    pod 'SBJson', '~> 2.6.0'
    
    platform :ios , '7.0'
    pod 'AFNetworking'

- 不同的target使用完全不同的Pods依赖库

CocoaPodsTest这个target使用的是Reachability、SBJson、AFNetworking三个依赖库，但Second这个target只需要使用OpenUDID这一个依赖库，这时可以使用target关键字，Podfile的描述方式如下：

    target :'MyApp' do  
    platform :ios    
    pod 'Reachability',  '~> 3.0.0'    
    pod 'SBJson', '~> 4.0.0'    
    
    platform :ios, '7.0'    
    pod 'AFNetworking', '~> 2.0'  
    end  
    
    target :'MyApp pro' do  
    pod 'OpenUDID', '~> 1.0.0'  
    end  

- over

Cocoapods与持续集成

集成Cocoapods后对项目的持续集成的影响。项目采用OClint进行持续集成，集成Cocoapods之后，该将哪些文件加入到持续集成中。

Cocoapods与项目SDK化

MyApp项目中集成了Cocoapods，使用Cocoapods管理第三方库公共库，类似AFNetworking、MJExtension等，同时使用私有Pod管理MyFoundation等私有组件。并且建立私有Pod管理单品类业务。因此主App中Pods分为三部分：

- 开源第三方库 
- MyApp私有组件库（Foundation、Catogery等）
- 单品类业务(MyApp主要涉及酒店模块、机票模块等)

对于这几部分，Cocoapods会将Pods打成libPods.a，独立模块供主App使用。

遇到的一个问题是，因为项目中存在金融模块的SDK（部分模块已经SDK化），而金融业务代码用到了AFNetworking，金融业务代码使用AFNetworking的方式是，主App给金融SDK一个AFNetworking的framework，金融使用该framework后可以调试相关代码，但是在金融打成framework给主App时不勾选AFNetworking这个target。因此具体的是：

    金融SDK -->link --> 主App AFNetworking(AFN手动导入主App).

到目前为止，没有导致任务问题。

然后如果集成Cocoapods后，AFNetworking放在Pods中管理，而Pods被Cocoapods打成了podlib.a，.a与.framework的访问就会出问题， .framework无法link到.a里的AFNetworking，最终导致link错误。对于这个问题，估计有多种可行的方案：

1. 方案一：给金融模块提供 AFNetworking.a 而不是 AFNetworking.framework。
2. 方案二：金融模块打framework时剔除AFNetworking ，在其打成framework时不勾选 AFN相关文件。（这种方式通过检验，因为金融模块不需要 link AFNetworking.framework）。
3. 方案三：暂未定

over。

Cocoapods与App打包

目前我们App提供三种打包方式，第一是Xcode打包，第二是ipa_genator.sh脚本打包，第三是测试用jenkins打包。集成Cocoapods后，这三种打包方式相关配置需要更改，否则不支持打包或者打包失败。

- Xcode打包，只需要使用MyApp.xcworkspace来编译运行程序，就可以打包成功。
- 脚本打包，需要更改相关配置，更改如下：

    #config.cfg文件为脚本打包路径配置文件，之前的路径指向的是MyApp.xcodeproj,我们需要将PROJECT_NAME改为：
    PROJECT_NAME="MyApp.xcworkspace"
    #因为每次打包需要首先build,而项目集成Cocoapods后，build路径是xcworkspace而不是原来的xcodeproj,因此需要指定正确的路径。

    #其次，我们需要将ipa_generator.sh文件中的
    
    PROJ_EXT=".xcodeproj" #此处改为 PROJ_EXT=".xcworkspace" 
    
    xctool_archive()
    {
    xctool -project $1 \  # 此处改为xctool -workspace
    -scheme $2 \
    -reporter json-compilation-database:compile_commands.json \
    -sdk iphoneos \
    archive \
    -archivePath $3
    }
    
    xcodebuild_archive()
    {
    xcodebuild -project $1 \ # 此处改为xctool -workspace
    -scheme $2 \
    -sdk iphoneos \
    archive \
    -archivePath $3 \
    | tee xcodebuild.log
    }

over.

- jenkins打包失败的问题。
- 

Cocopods集成时间表

1. 将原来的手动添加的第三方库迁移到现在的Cocoapods管理第三方库，需要将原来第三方库的配置相关的文件删除干净。Cocoapods会将第三方库的所有需要配置的静态库或者文件集成在libPods.a静态库中，在Build Phases中可以找到这个.a静态库。鉴于这个考虑，我们需要删除原来手动添加的第三方库的配置文件。
2. 纪录所有的原来的第三方库的配置文件，一并删除

- AFNetworking
- FLEX
- FMDB
- ...

Cocoapods常见问题汇总

1. Pods依赖库已经更新
2. Cocoapods无法安装的问题。

Mac系统升级到10.11使用Cocoapods出现pod:command not found 解决方案

    $ sudo gem install -n /usr/local/bin cocoapods

如果podupdate 或者pod install 还卡在哪儿，则

1. pod install 或者pod update 速度慢的问题

    查看ruby源，更换为淘宝镜像，如果还出现问题，则
    原因可能是：当执行以上两个命令时会升级Cocoapods的Specs仓库，加一个参数就可以省略这一步，然后速度可能会提升。参数命令如下：
    pod install --verbose --no-repo-update
    pod update --verbose --no-repo-update 
    执行完命令以后有时还是会报错，不妨更新一下本地repo
    pod repo update 

1. 这个问题是比较常见的问题

    "podfile.lock" not such file or directory......

http://stackoverflow.com/questions/17072396/cocoapods-errors-on-project-build

1. 集成Cocoapods编译通过但是链接不通过的问题。

    Undefined symbols for architecture x86_64:
    
    "_OBJC_CLASS_$_AFNetworking", referenced from:
    
    objc-class-ref in HJCXMPPTools.o
    
    ld: symbol(s) not found for architecture x86_64
    
    clang: error: linker command failed with exit code 1 (use -v to see invocation)

小编发现，小编之前是手动导入的AFNetworking这个第三方库，小编估计是手动导入时更改了某些配置文件导致与Cocoapods集成时的配置文件发生冲突，导致无法link。于是小编在此用Cocoapods更新框架时，发现终端的一段警告：

    [!] The `myQQ [Debug]` target overrides the `OTHER_LDFLAGS` build setting defined in `Pods/Target Support Files/Pods/Pods.debug.xcconfig'. This can lead to problems with the CocoaPods installation
    
    - Use the `$(inherited)` flag, or
    
    - Remove the build settings from the target.

这下的思路应该是，手动导入AFN时修改了某些配置，修改了Other Linker Flags。

所以解决方案是：

按照提示，在Build setting里的Other Linker Flags增加$(inherited)。喜大普奔，这个错误算是解决了。

over.

1. 需要加入gitignore的文件：
2. 对MyApp包大小的影响、对编译时间和编译速度的影响
3. 如何更新第三方库
4. 第三方库与项目代码中的文件重名冲突
5. 第三方库的sdk版本与项目sdk版本不符时解决方案
6. Cocoapods软件版本更新
7. Cocoapods 目前版本为 0.39.0， 最新的1.0.0为测试版
8. 当两个库同时需要使用AFNetworking时，Cocoapods会确定一个同时能被这两个库使用的版本，然后将同一个安装版本链接到两个不同的库中。
9. 安装pod install 第三方库过程中，Cocoapods会使用递归来分析所有的需求，并且建立一个代码相关性的图，最后将Podfile序列化为Podfile.lock
10. 选择的文件夹对：

    Could not automatically select an Xcode project. Specify one in your Podfile like so: xcodeproj 'path/to/Project.xcodeproj'

问题出在你选择的项目文件夹不对，应该选择Iphone文件夹集成Cocoapods

1. 如果项目中有与Pod同名的文件。编译不会通过。
2. 对于那些不在Cocoapods公共Git仓库中的库，可以用任何一个Git,Mercurial或者是SVN仓库取代，并且还可以指定具体的commit，branch或者tag。 即私有pod。

    pod 'Y', :git => 'https://github.com/NSHipster/Y.git', :commit => 'b4dc0ffee' 

1. over.

私有Pods(进阶篇)

私有Pod Spec repo

Spec Repo 是所有Pods的一个索引，就是一个容器，所有公开的Pods都在里面，实际上是Git仓库，remote端在Github上，当你使用了Cocoapods后它会被clone到本地的 ~/.cocoapods/repos目录下，可以进入这个目录下，有一个master文件夹，这个文件夹就是官方的 Spec Repo 了。目录的结构是这样的 . -->Specs-->[SPEC_NAME]-->[VERSION]-->[SPEC_NAME].podspec 因此，如果我们要创建自己的类似于master的私有Spec Repo ,这里我们可以fork官方的Repo，也可以自己创建，个人建议不fork，因为你只是想添加自己的Pods,没有必要把现有的公开的Pods都copy一份，所以创建一个Git仓库，这里我选择Coding，因为Coding可以创建私有库。也可以自己搭建服务器，创建私有库。 

在coding上创建自己的spec私有仓库，本地跟踪管理

首先我们在coding上创建一个自己的spec仓库，用来管理自己私有组件的spec文档。

这是我创建好的Pods spec仓库

    这是我Coding上私有Pods Spec仓库链接：https://coding.net/u/schiller/p/MyDemoSpec/git

创建完成后在终端执行如下命令

    $ pod repo add [Private Repo Name] [Github HTTPS clone URL]
    例如我创建的MyDemoSpec： pod repo add MyDemoSpec https://git.coding.net/schiller/MyDemoSpec.git

pod 命令本身在git管理下，所以执行 pod repo add之后就会在cocoapods/repos仓库中新建仓库跟踪远程仓库。如果创建成功的话，进入到 ~/.cocoapods/repos目录下就可以看到MyDemoSpec这个目录了。第一步完成。

注意：如果其他人共同使用这个私有SpecRepo的话在他对应Git仓库的权限的前提下执行相同的命令添加这个Spec Repo 即可。

over.

创建自己的私有组件

通过pod创建自己私有组件库，步骤如下：

- 首先将组件从项目中拆分出来，组建自己的组件库
- 然后在终端用 一下命令创建一个pod文件

    $ pod lib create PodLibrary 

- 向Pod文件夹中添加库文件和资源，然后配置podspec文件，模块文件放在Pod/Classes中，然后执行 pod update 命令
- 每次向Pod添加了新的文件或者以后更新了podspec的版本都需要重新执行一遍pod update命令
- 测试无误后，将项目推送到远程仓库，并编辑podspec

    $pod repo push MyDemoSpec PodLibrary.podspec #前面是本地Repo名字，后面是podspec名字

- 编辑podspec，podspec文件的格式和定制如下：

    Pod::Spec.new do |s|
    s.name             = "PodLibrary"
    s.version          = "0.1.0"
    #项目总结
    s.summary          = "My PodLibrary is a repo for UIButton+Positioning assembly."
    #项目描述，貌似描述的文字要多于总结的，否则会报警告
    s.description      = <<-DESC
    My Private UIButton+Positioning assembly
    * MarkDown format 
    * It is a nice world!
    
    DESC
    #项目的主页，要求可以访问
    s.homepage         = "https://git.coding.net"
    #项目的证书类型，一开始在git 就要配置好
    s.license          = 'MIT'
    s.author           = { "gaolong" => "gaolong@alibaba.com" }
    #最重要的一部分，项目的源地址，目前只支持Http\Https, 不支持SSH，小编曾经犯了一个低级错误，将这个source指向了以前建立的一个Spec.git,然后一直报"The `PodLibrary.podspec` specification does not validate"的错误，小编又不会调试，所以很丢人的折腾了两个小时。
    s.source           = { :git => "https://git.coding.net/schiller/MyDemoSpec.git", :tag => "0.1.0" }
    s.platform     = :ios, '7.0'
    s.requires_arc = true
    #表示项目的源文件在Pods/Classes文件夹下
    s.source_files = 'Pod/Classes/**/*'
    s.resource_bundles = {
    'PodLibrary' => ['Pod/Assets/*.png']
    }
    
    #项目的依赖库等等
    # s.public_header_files = 'Pod/Classes/**/*.h'
    # s.frameworks = 'UIKit', 'MapKit'
    # s.dependency 'AFNetworking', '~> 2.3'
    end

编辑podspec文件之后，需要验证该文件是否可用，如果有任何WARNING 或者ERROR都是不行的，它就不能被添加到Spec repo ，需要执行以下代码验证

    $ pod lib lint 

当你看到

    -> PodLibrary (0.1.0)
    PodLibrary passesd validation

说明验证通过了。

- 配置私有组件项目

1. 配置.gitignore

每次记住修改完.podspec文件后要上传到本地Specs仓库，pod push，会自动push到远端仓库；要给私有组件项目配置.gitignore,否则私有项目的维护会很困难私有项目gitignore配置如下：

    ```
    

1. 私有组件项目配置依赖

    s.dependency即为私有组件配置依赖，包括第三方依赖库和静态库，静态文件、配置非arc等。其中依赖库的版本如 AFNetworking 的版本要和项目中的项目中的AFNetworking版本兼容，否则会出现版本不一致的报错，无法给项目安装该私有组件库。例如：-｀AFNerworking(= 3.0.4)` required by `Podfile`- `AFNerworking(= 3.0.4)` required by `Podfile.lock`- `AFNerworking(~> 2.3)` required by `podTestLibrary(1.0.0)`显然是两个版本不符，pod不知道加载哪一个了。

1. over.

- 本地测试PodSpec文件

我们可以创建一个新的项目，这个项目的Podfile 文件直接指定刚才创建好的podspec文件，看是否可用（我这里选Demos ,这个我的测试项目），在Podfile中我们可以这样编辑，有两种方式

    platform :ios, '7.0'
    pod 'PodLibrary', :path => '/Users/schiller/Desktop/PodLibrary/PodLibrary.podspec' #指定podspec 文件
    #或者这样
    pod 'PodLibrary', :path => '/Users/schiller/Desktop/PodLibrary' #指定路径

然后执行pod install 命令安装第三方库，就像安装AFNetworking那样安装自己的公共组件库，然后发现库文件被加到Pods子项目去了。测试无误后进行下一步，提交podspec到Spec repo中。

- 向Spec Repo提交podspec

回顾一下我们1,2两步所做的工作，接下来我们向我们的私有Spec Repo提交podspec，只需要一个命令，要cd 到私有项目的目录下执行该命令

    $pod repo push MyDemoSpec PodLibrary.podspec #前面是本地Repo名字，后面是podspec名字

如果pod repo push 出现报错:

    [!]The `PodLibrary.podspec` specification does not validate 

多半是你的podspec文件配置不成功。

小编在第三步执行pod lib lint 命令，一直显示该文件pass validate ,但是将 私有项目push 到 本地spec仓库时出现以上报错，于是小编机智地执行一下命令

    $pod repo push MyDemoSpec PodLibrary.podspec --allow-warnings 

允许报警，然而并没有起到作用

于是小编查看详细日志

    $pod repo push MyDemoSpec PodLibrary.podspec --verbose

当小编看到错误原因是自己将s.source源地址写成了之前的源地址的时候，小编内心是崩溃的，QAQ。

- 通过$ pod lib lint 命令查看私有组件库是否通过验证的问题，
- 所以首先一定要确定自己的podspec通过验证无误，再一个就是删除无用的注释，尽量规范，完成之后，这个名叫PodLibrary的组建就添加到了我们的私有Spec Repo ,可以进入~/.cocoapods/repos/MyDemoSpec目录下查看

    在MyDemoSpec目录下，有PodLibrary文件夹，下面有0.1.0文件夹，PodLibrary.podspec

- 然后我们执行pod search ,就会发现自己的库可以搜到了。

    $ pod search podTestLibrary
    -> podTestLibrary (1.0.0)
    XTURLRequest Assembly
    pod 'podTestLibrary', '~> 1.0.0'
    - Homepage: https://coding.net/u/schiller/p/podTestLibrary
    - Source:   https://git.coding.net/schiller/podTestLibrary.git
    - Versions: 1.0.0 [GLSpecs repo] - 0.1.0 [master repo] 

- over.

使用制作好的Pod

完成一系列操作，就可以使用自己制作好的pod了，回顾一下，就可以弄清楚这个Pod私有库的原理了。

- 私有Pod原理详解

    原理：
    有一个私有项目(私有组件，PodLibrary)，该私有项目的.podspec文件有s.source源指向coding的私有Git仓库；在自己的Cocoapods目录下 ， 即~/.cocoapods/repos 仓库下有一个存放私有库链接的Spec(MyDemoSpec，同样要给这个私有的MyDemoSpec建立远端仓库),该spec用来管理私有组件的.podspec，所以私有组件的.podspec文件要push到MyDemoSpec上去；
    然后某个项目需要用到私有库，只需要在podfile中加上MyDemoSpec的链接，然后pod update 就实现了将私有组件加载到需要的项目中。每次pod install 时，pod会根据私有spec仓库的.podspec文件找到PodLibrary这个私有组件的链接，然后将PodLibrary私有组件download到项目中，以Pod以来库的形式实现。
    同样的道理，之前的公有库的实现方式只是通过~/.cocoapods/repos中的master spec库寻找到需要的存放在Cocoapods上的公有项目，因为master中存放了所有的Cocoapods支持的第三方库的链接，每次pod install ,pod会去master找AFNetworking 的source源链接，该源链接在cocoapods上，然后download下载下来，就在项目中集成了AFNetworking Pods依赖库.
    验证：cd 到~/.cocoapods/repos , cd 到master ,cd到Specs,ls -a ,会发现将近6000个公有库，然后cd 到AFNetworking ,ls -a 发现AFNetworking的所有版本，然后cd 到某个版本，发现AFNetworking.podspec.json 文件。就是传说中的.podspec配置文件，然后查看配置，在s.source一栏中发现"git": "https://github.com/AFNetworking/AFNetworking.git",这就是spec保存的AFNetworking 组件的源地址，pod根据spec文件保存的这个地址找到源文件，然后就是pod install 安装AFNetworking 了。

- 我们在项目中可以这样使用自己的私有组件Pod,我们在podfile中这么写，然后再pod install ，就会发现项目中的Pods有PodLibrary这个组件了。

    platform :ios, '7.0'
    inhibit_all_warnings!
    
    #公有库
    source 'https://github.com/CocoaPods/Specs.git'
    
    pod 'MBProgressHUD', '0.9.1'
    
    #私有库
    source 'https://git.coding.net/schiller/MyDemoSpec.git'
    
    pod 'PodLibrary', '~> 0.1.0'

- 至此，私有pod集成完毕
- over.

over.

私有Pod协同合作

1. 对于私有pod的协同合作非常简单，Spec仓库放置在~/.cocoapods/repos目录下，自己的Spec仓库放置在coding或者github上，因此只需要执行

    $ pod repo add MySpecs https://git.coding.net/schiller/MySpecs.git

~/.cocoapods/repos 就会有这个仓库，该仓库指向我们的私有组件的位置，因此在终端执行

    $ pod search podTestLibrary

就能搜索到我们自己的私有组件。只需要在我们的项目中pod install就可以使用该私有组件了。

1. 

删除私有库

通过如下命令删除

    $ pod repo remove MyDemoSpec

over.

部署到公有库

代码创建好了，当然可以提交到公有库了，比如想让大伙都来用自己的组件，这个对个人和团队的影响力是有一定提升的。之前需要审核一两天的时间，现在有了trunk，提价即可使用了。

1. 首先将代码上传到github
2. 注册trunk服务

    pod trunk register wskrdfeai@gmail.com 'laughmaker' --description='我爱我的因我觉得欢喜' --verbose

1. 可查看注册信息

    pod trunk me 

1. 将仓库上传到公有库中心

    pod trunk push MyDmeoSpec

1. 也可以将其他人添加进来一起开发

    pod trunk add-owner ProjectName(项目名) email(要添加进来的人)

1. 至此添加完毕，其他人直接在podfile中就可以搜索和添加你的公有项目了。

提供私有pod测试项目

大家可以通过我已经建立好的项目来调试私有pod，熟悉私有库建立方法，这是小编的项目。（密码可以私戳我）

    Specs的仓库:  https://git.coding.net/schiller/GLSpecs.git
    私有组件仓库:  https://git.coding.net/schiller/podTestLibrary.git
    测试Cocoapods的demo: https://github.com/schillerGao/MyGLDemos.git 

over.

私有组件建立目录层级（配置subspec）

通过上述方式配置的spec文件我们发现，Cocoapods会将所有文件都放在一个文件夹下，并没有建立层级管理，导致主App Pods代码过于冗杂。一个好的方式是使用subspec ，拆分各模块的依赖关系。

    Pod::Spec.new do |s|
    
    s.name         = "XTFoundation"
    s.version      = "0.0.7"
    s.summary      = "小图项目组iOS端基础模块"
    s.homepage     = "https://coding.net/u/xiaotujieshu/p/XTFoundation_iOS"
    s.license      = { :type => "MIT", :file => "LICENSE" }
    s.author       = { "hezhendong" => "wskrdfeai@gmail.com" }
    s.platform     = :ios, "7.0"
    s.source       = { :git => "https://git.coding.net/xiaotujieshu/XTFoundation_iOS.git", :tag => s.version }
    s.frameworks = "UIKit"
    
    s.subspec 'XTNetwork' do |network|
    	network.source_files = 'XTFoundation/XTNetwork/**/*'
    end
    
    s.subspec 'XTMacro' do |macro|
    macro.source_files = 'XTFoundation/XTMacro/**/*'
    end
    
    s.subspec 'XTHUD' do |hud|
    hud.source_files = 'XTFoundation/XTHUD/**/*'
    end
    
    s.subspec 'XTRegular' do |regular|
    regular.source_files = 'XTFoundation/XTRegular/**/*'
    end
    
    s.subspec 'XTSNS' do |sns|
    sns.source_files = 'XTFoundation/XTSNS/**/*.{h,m}'
    sns.resources = "XTFoundation/XTSNS/Resources/*.png"
    sns.dependency  'OpenShare', '~> 0.0.1'
    end
    
    s.subspec 'XTUIUtils' do |uiUtils|
    uiUtils.source_files = 'XTFoundation/XTUIUtils/**/*.{h,m}'
    end
    
    end

over.

Cocoapods番外篇

Cocoapods制作篇，创建属于自己或者团队的开源项目，供他人使用

可以通过创建自己的依赖库，用来提升个人实力和影响力，或者团队实力和影响力。

使用Cocoapods里没有的第三方库新版本（奇巧淫技）

我们以PlayHavenSDK为例，我们在Cocoapods/Specs这个仓库可以看到，PlayHavenSDK最新版本为1.11.0。而playhaven / sdk-ios里面的tag，已经有了1.12.1这个版本。CocoaPods的Spec repository已经落后了两个版本。这时我们想使用最新版，需要的就是一个更新的spec。

    Pod::Spec.new do |s|
    s.name = 'PlayHavenSDK'
    s.version = '1.12.1'
    s.license = 'MIT'
    s.summary = 'PlayHaven is a real-time mobile game marketing platform to help you take control of the business of your games.'
    s.homepage = 'http://playhaven.com'
    s.author = { 'Sam Stewart' => 'sam@playhaven.com' }
    s.source = { :git => 'https://github.com/playhaven/sdk-ios.git', :tag => '1.12.1' }
    s.description = "PlayHaven is a real-time mobile game marketing platform to help you take control of the business of your games. Acquire, retain, re-engage, and monetize your players with the help of PlayHaven's powerful marketing platform. Integrate once and embrace the flexibility of the web as you build, schedule, deploy, and analyze your in-game promotions and monetization in real-time through PlayHaven's easy-to-use, web-based dashboard. An API token and secret is required to use this SDK. These tokens uniquely identify your app to PlayHaven and prevent others from making requests to the API on your behalf. To get a token and secret, please visit the PlayHaven developer dashboard at https://dashboard.playhaven.com."
    s.platform = :ios
    # PlayHaven includes prefixed versions of SBJson and OpenUDID
    s.source_files = 'Cache', 'src', 'WaterWorks', 'JSON', 'OpenUDID'
    s.frameworks = 'SystemConfiguration', 'CFNetwork', 'StoreKit', 'CoreGraphics', 'QuartzCore'
    s.weak_frameworks = 'AdSupport'
    end

这里我们注意到s.source这一行配置，指向了playhaven/sdk-ios.git仓库的1.12.1这个tag，表示这个spec将使用这个tag的代码。我们将它保存到本地的一个目录中，如~/Desktop/PlayHavenSDK/PlayHavenSDK.podspec。我们回到Xcode project目录中，打开Podfile，添加pod 'PlayHavenSDK', :podspec => '~/Desktop/PlayHavenSDK/PlayHavenSDK.podspec'，接着运行pod update即可。

PS: 当然这个podspec也可以不放在本地，比如放在gist上。

Cocoapods实战篇记录

在项目切一个cocoapodsDev分支，用来检测使用cocoapods管理第三方库和本地库的一些bug，提前预演，如果能够完全弄好，就迁移到dev分支中。

MyApp当前项目架构

当前项目结构中没有使用Cocoapods管理，使用子模块和framework管理独立业务代码，一方面项目架构不够清晰，另一方面调试相当困难。

迁移测试

首先将baseSDK做成私有Pod、设置好specs文件，然后主App中的删除baseSDK的依赖，然后实现了。pod install主App，于是就集成了Cocoapods。

集成过程中，出现了一个非常诡异的问题，编译通过，但是linker不到Pods集成的库：

    linker command failed.

我们查看发现link不到的原因：

1. 酒店和金融SDK化，打成了Framework，导致linker不到我们的Pods。
2. 第二个原因，在Cocoapods install 第三方库时项目target覆盖了Pods-debug.xcconfig ,导致是没有配置好：http://www.bkjia.com/IOSjc/1017630.html
3. 

over

OpenURL实现组件之间的通讯

待完善
