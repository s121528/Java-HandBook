# 1 认识IntelliJ IDEA

## 1.1 说明

如果说IntelliJ IDEA是一款现代化智能开发工具的话，Eclipse则称得上是石器时代的东西了。其实笔者也是一枚从Eclipse转IDEA的探索者，随着近期的不断开发实践和调试，逐步体会到这款智能IDE带来的巨大开发便利，在强大的插件功能支持下，诸如对Git和Maven的支持简直让人停不下来，各种代码提示，包括JS更是手到擒来，最终不得不被这款神奇的IDE所折服。为了让身边更多的小伙伴参与进来，决定写下这篇文章，与君共享。经过前期的不断练习，后端开发之前使用的是eclipse和STS，注意eclipse中使用时快捷键失效的（禁用搜狗输入法的快捷键）。但是后期开发过程中我会选择idea作为开发工具使用。Eclipse通常用于实际项目的开发，STS用于Spring Boot和Spring Cloud的项目开发和进行框架的搭建，IDEA用于练习相应的新技术（[JDK8](https://v2.fangcloud.com/share/b7b5cc34dd46d5b0a61f3d1d5a)、[JDK9](https://v2.fangcloud.com/share/af36de3c0e21c9ca257fda3b0f)）。如果想获得更多软件工具可以查看[开发工具](https://www.jianshu.com/p/fb0737c88083)文章。

## 1.2 下载

> 高级传送门：[IDEA 终极版下载地址](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows)，本文中所以的操作和实列都是在2020.2.2版本下！

## 1.3 配置默认路径

很多软件默认安装路径都是C盘符，但是本人实在看不下去软件东西都放在C盘。

进入到idea的安装目录，idea.properties文件，添加自己指定的路径地址。

```properties
idea.config.path=${idea.home.path}/user/config
idea.system.path=${idea.home.path}/user/system
```

## 1.4 激活

关于这个“key is invalid”的问题，在网站（www.jiweichengzhu.com）上已经写了好几篇文章了，从早期的idea版本到2020的版本，但是有很多人都不仔细看，也从来不会做总结。归根到底，无外乎就两种可能（看仔细了）。

1、激活补丁不是最新的版本；

2、之前的配置文件没有清理干净；

C:\Users\guodd\AppData\Local\JetBrains

激活失败，以及出现“key is invalid”问题的很大概率所在，看好了我图中的目录，是Local，而不是Roaming，网上很多人的教学，上来就是让你们删Roaming文件夹，这个其实并不是必须的，基本上将这个Local删了就能解决一大半，如果依旧还是没能搞定，那再删除Roaming不迟。

我为什么反对你们上来就删除Roaming目录？

因为这个Roaming目录里面存放了很多的配置信息，包括你的一些快捷键设置、代码格式等等，如果你上来就直接删除了，那痛苦的只有你自己，就算是非要删除，也务必要将配置做好存档，导出settings.jar。

## 1.5 工具的看法

思想是**战略**上的高度，工具是**战术**上的高度！！！

# 2 IDEA & Eclipse

![对比](https://upload-images.jianshu.io/upload_images/8185387-459f402d8321801c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/648/format/webp)

## 2.1 IntelliJ IDEA 

> 谁用谁知道，话不多说！！！

## 2.2 Eclipse (STS)

>初学者可以多用，提高代码的编写能！

# 3 全局环境配置

## 3.1 JDK

IDEA中的JDK的位置：SDKs、Project、Modules、Java Compiler、Runner（Use project Jdk）

[JDK参考资料](https://segmentfault.com/a/1190000018708356)

1、单个JDK配置（maven的settings中指定）

```xml
<profiles>
    <profile>
        <id>jdk-1.8</id>
        <activation>
            <activeByDefault>true</activeByDefault>
            <jdk>1.8</jdk>
        </activation>
        <properties>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
        </properties>
    </profile>
</profiles>
```

2、多JDK配置（maven的settings中指定）

```xml
<profiles>
    <profile> 
        <id>java8-compiler</id>
        <!-- activeByDefault=true代表如果不指定某个固定id的profile，那么就使用这个环境 -->  
        <activation> 
            <activeByDefault>true</activeByDefault> 
        </activation>
        <properties> 
            <JAVA_HOME>D:\dev\soft\JDK\jdk8u114</JAVA_HOME>  
            <JAVA_VERSION>1.8</JAVA_VERSION>  
            <maven.compiler.source>1.8</maven.compiler.source>  
            <maven.compiler.target>1.8</maven.compiler.target>  
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion> 
        </properties> 
    </profile>

    <profile> 
        <id>java11-compiler</id>  
        <properties> 
            <JAVA_HOME>D:\dev\soft\JDK\jdk-11.0.6</JAVA_HOME>  
            <JAVA_VERSION>11</JAVA_VERSION>  
            <maven.compiler.source>11</maven.compiler.source>  
            <maven.compiler.target>11</maven.compiler.target>  
            <maven.compiler.compilerVersion>11</maven.compiler.compilerVersion> 
        </properties> 
    </profile>
</profiles>
```

## 3.2 Maven

> File | Settings | Build, Execution, Deployment | Build Tools

1、官网下载maven，下载地址：[Apache](http://apache.org/)。

2、配置环境变量

settings.xml中配置maven镜像地址

推荐一：阿里云的镜像站（首推，新站，速度暴快）

```xml
<!—1.配置jar包的仓库地址-->
<localRepository>D:/mavenrepository</localRepository>

<!—2.配置jar包的下载镜像地址-->
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

推荐二：spring-boot和spring-cloud等版本比较新，所以目前国内的一些仓库还没有新版本的jar

```xml
<mirror>
      <id>spring-milestones</id>
      <name>Spring Milestones</name>
      <url>https://repo.spring.io/libs-milestone</url>
      <mirrorOf>central</mirrorOf>
</mirror>
```

注意：Setting.xml文件镜像地址：通常将其复制出来一份到mavenRepository目录下。（方便版本更换）

[Jar包下载地址01](https://mvnrepository.com/)

[Jar包下载地址02](https://www.mvnjar.com/)

[Jar包下载地址03](http://search.maven.org/)

3、插件配置

```xml
<plugins>
    <!-- maven插件 -->
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>${spring-boot.version}</version>
    </plugin>
    <!-- 编译级别 -->
    <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
            <target>${java.version}</target>
            <source>${java.version}</source>
            <encoding>UTF-8</encoding>
        </configuration>
    </plugin>
    <!-- 跳过单元测试 -->
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <configuration>
            <skip>true</skip>
        </configuration>
    </plugin>
</plugins>
```

4、boot项目打包配置

```xml
<build>
	<finalName>${project.artifactId}</finalName>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<executions>
				<execution>
					<goals>
						<goal>repackage</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
	</plugins>
</build>
```

5、镜像地址

```xml
<repositories>
    <repository>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </repository>
</repositories>
```

6、idea中指定默认配置，project.default.xml文件

```properties
<component name="MavenImportPreferences">
    <option name="generalSettings">
        <MavenGeneralSettings>
            <option name="mavenHome" value="D:\dev\maven\maven-3.6.3" />
            <option name="localRepository" value="D:\dev\maven\maven-repository" />
            <option name="userSettingsFile" value="D:\dev\maven\maven-3.6.3\conf\settings.xml" />
        </MavenGeneralSettings>
    </option>
</component>
```

## 3.3 Gradle

1、下载安装

2、配置环境变量（GRADLE_HOME、GRADLE_USER_HOME）

3、安装检验：gradle -v

4、配置IDEA

## 3.4 SVN

1、下载客户端（服务端，非必须）

2、安装客户端（注意打勾，否则找不到exe文件）

3、安装检查：右键鼠标

4、配置IDEA

## 3.5 Git

1、下载

2、安装配置

3、安装检查：git version

4、配置IDEA

5、命令行执行：git config --global http.postBuffer 524288000 

## 3.6 编码乱码

1、项目源代码中文乱码：Settings > Editor > File Encodings

2、Main方法运行，控制台中文乱码：Settings > Build, Execution, Deployment > Compile > Java Compiler > Additional command line parameters > 设置为：-encoding utf-8

3、Tomcat运行，控制台中文乱码：Edit Configurations > Tomcat Server > server > VM options > 设置为：-Dfile.encoding=UTF-8

4、如果还乱码，继续往下设置：idea > Help 菜单 > Edit Custom VM Options…菜单，编辑配置文件，在末尾添加：-Dfile.encoding=UTF-8

## 3.7 启动视图

Run Dashboard运行视图，修改项目.idea文件夹下的workspace.xml文件，在接下来找到 <component name="RunDashboard">中添加下面代码。

```xml
<option name="configurationTypes">  
      <set>  
        <option value="SpringBootApplicationConfigurationType" />  
      </set>  
 </option>
```

新版本默认带的有就无需配置了！！！

# 4 局部环境配置

## 4.1 字体和[主题01](http://www.riaway.com/)和[主题02](http://color-themes.com/?view=index)

窗口字体：File | Settings | Appearance & Behavior | Appearance（勾选字体，选择自己的字体和大小11）

编辑文件字体：File | Settings | Editor | Font（勾选覆盖字体，选择自己的字体和大小12）

file => import setttings => 选中1中下载的主题jar文件 => 一路确认 => 重启

## 4.2 自动编译

> File | Settings | Build, Execution, Deployment | Compiler（build project automatically）

启动的项目需要切换下应用，再回到idea即可自动编译程序。

## 4.3 智能导包

> File | Settings | Editor | General | Auto Import（Optimize imports on the fly(for current project)）

## 4.4 忽略大小写

> File | Settings | Editor | General | Code Completion（match case去掉打勾）

## 4.5 悬浮提示开关

> File | Settings | Editor | General  |（Show quick documentation on mouse move）

## 4.6 取消单行显示tabs

> File | Settings | Editor | General | Editor Tabs（去掉Show tabs in one row的勾选）

## 4.7 行号显示

> File | Settings | Editor | General | Editor Tabs

## 4.8 插件下载去掉https

> File | Settings | Appearance & Behavior | System Settings | Updates

## 4.9 mybatis的xml警告背景

> 第一步：File | Settings | Editor | Inspections，去掉SQL | No data sources configured和SQL | SQL dialect detection对应的对号。
>
> 第二步：File | Settings | Editor | Color Scheme | General，选择code的Injected language fragment然后去除background。

## 4.10 方法参数提示

File | Settings | Editor | General | Code Completion（Parameter Info对应的选项都打勾）

## 4.11 用\*标识编辑过的文件 

```properties
1.Editor–>General –> Editor Tabs
2.在IDEA中，你需要做以下设置, 这样被修改的文件会以*号标识出来，你可以及时保存相关的文件。
3.“Mark modifyied tabs with asterisk
```

## 4.12 关闭自动代码提示

Preferences => IDE Settings => Editor => Code Completion => Autopopup documentation in (ms)

## 4.13 修改高亮

File | Settings | Editor | Color Scheme | General

- code=>Identifier under caret，修改跟随一致变量的颜色
- code=>Identifier under caret (write)，修改单击选中的颜色
- Edit=>Selection background，修改双击选中的颜色

## 4.14 数据库全局设置

File | Settings | Languages & Frameworks | SQL Dialects选用你的数据库类型

Global Data Sources

![image-20201011092701841](../插图/image-20201011092701841.png)

# 5 版本控制配置

## 5.1 SVN

### 5.1.1 基础配置

1. 安装SVN客户端

   特别注意：安装过程需要选中第二项command line tools的will be installed on local hard dirve

2. 屏蔽文件：*.iml;.idea;.classpath;.project;.settings;target;logs;

   File | Settings | Editor | File Types

3. SVN Disconnect插件安装

## 5.2 Git

### 5.2.1 Gitlab

1. 安装插件

   >Gitlab

2. 配置Gitlab服务地址

   >进行Gitlab服务地址的配置

   ![配置Gitlab服务地址](../\插图\Windows操作系统\配置GitLab服务器地址.png)

3. 申请Token

   >

4. 创建项目提交

### 5.2.2 GitHub



### 5.2.3 Gitee



### 5.2.4 分支/合并

```
右下角Git标志，点击New Branch，创建分支。
右下角Git标志，点击local Branchs，进行分支切换。
右下角Git标志，先切换到master分支，点击Remote Branch，进行分支合并到master。
```

### 5.2.5 Tag标签

```properties
// 推送Tag到远程
git push origin v1.0.0.0      
// 删除本地tag
git tag -d v1.0.0
// 删除已经远程的tag
git push origin :refs/tags/v1.0.0
注意：可以通过提交代码时进行tag的提交。
```

# 6 快捷键配置

## 6.1 eclipse快捷键

> File | Settings | Keymap

## 6.2 同步eclipse

1. 快捷键的配置，习惯了eclipse快捷键设置。（completion、close）
   智能提示：File->Settings-> Keymap-> 搜索completion basic -> 给Basic添加快捷键为Alt+/。
   关闭窗口：File->Settings-> Keymap-> 搜索editor tabs close-> 添加快捷键为Ctrl+W。
   格式代码：File->Settings-> Keymap-> 搜索Reformat Code-> 添加快捷键为Ctrl+Shift+F。

2. 快捷键的配置，习惯了Navicat Premium数据库管理工具的使用（open console、execute）
   File->Settings-> Keymap->Other-open console-> 添加快捷键为Ctrl+Q。
   File->Settings-> Keymap->Other-Execute Current Statement in Multiline Console-> 添加快捷键为Ctrl+R。File | Settings | Languages & Frameworks | SQL Dialects->设置IDEA的数据库连接对象。

3. 按鼠标中键快速打开智能提示，取代alt+enter 
   File->Settings-> Keymap-> 搜索 Show Intention Actions -> 添加快捷键为鼠标中键。

4. 按F2快速修改文件名，告别双手操作。
   File->Settings-> Keymap-> 搜索 Rename -> 将快捷键设置为F2 。

5. 按F3直接打开文件所在目录，浏览一步到位。
   File->Settings-> Keymap-> 搜索 Show In Explorer -> 将快捷键设置为F3 。

6. Ctrl+ E 来找到最近访问的文件和Ctrl+ Shift + E 来访问最近编辑的文件

7. Ctrl+ F 进行相应的搜索
   File->Settings-> Keymap-> 搜索find（edit下的）-> 将快捷键设置为Ctrl+ F

8. 全局查询/替换操作

   Main menu | Edit | Find | Find in Path... -> 将快捷键设置为ctrl+h

   Main menu | Edit | Find | Replace in Path...  -> 将快捷键设置为ctrl+shift+h

9. File->Settings-> Keymap-> 搜索 Run context configuration-> 将快捷键设置为Ctrl+ F10 

10. 展开和折叠方法的快捷键

​       Main menu | Code | Folding | Expand All  折叠->alt+

​       Main menu | Code | Folding | Collapse All 展开->alt-

11. 翻译快捷键

    Plug-ins | Translation | Translate 翻译->ctrl+1

12. 跟踪 Java 源码的技巧

    Main menu | Navigate | Type Hierarchy -> 类继承图关系，将快捷键设置为 F4

    Main menu | Navigate | Call Hierarchy  -> 方法的调用链关系，将快捷键设置为 ctrl+alt+h

    Main menu | Navigate | find usages  -> 方法的调用位置，将快捷键设置为 ctrl+g

    Main menu | Navigate | File Structure -> 类里面有那些方法，将快捷键设置为 ctrl+o

# 7 Live Template

## 7.1 使用系统自带的模板

> File | Settings | Editor | Live Templates

## 7.2 自定义模板

> live template自定义（常用设置：main、psfi、psfs、pi、ps、pm）

```properties
/**
 * 属性描述：$VAR1$
 */
public String $VAR2$;

$END$


/**
 * 属性描述：$VAR1$
 */
private static final String $VAR2$ = "aa";

$END$
```

## 7.3 自定义类头

```properties
/**
 * The class/interface
 *
 * @author ${USER}
 * @version 1.0
 */
 
 /**
 * The class/interface
 *
 * @author ${USER}
 * @version 1.0 use jdk 1.8
 */
```

# 8 Postfix

## 8.1 常用模板

> postfix（for、sout、field、return、nn）

# 9 插件安装

## 9.1 Translation

>翻译工具。全局配置快捷键为ctrl+1，配置有道的翻译，音标的字体设置为Arial。

电脑安装的有翻译工具，就无须安装了。

## 9.2 Maven Helper
>maven管理工具。

![../插图\Windows操作系统\Idea-Maven-Helper.jpg)

## 9.3 Lombok

>get、set等pojo简写。

## 9.4 JRebel

https://www.2loveyou.com/articles/2020/01/09/1578533228431.html

https://www.guidgen.com/

>热部署，自动编译https://jrebel.qekang.com/bb25c9bf-7695-48d6-b1a0-baf893ca7631（激活地址）
>
>注意配置：一定要设置！！！离线工作（work offline）

## 9.5 RestfulToolkit

>快速查找rest full接口，sts中自带的有。

## 9.6 MyBatisCodeHelperPro

https://zhile.io/jetbrains-paid-plugins-license.html

https://plugins.jetbrains.com/plugin/14522-mybatiscodehelperpro-marketplace-edition-/versions

https://www.cnblogs.com/borber/p/MyBatisCodeHelper.html

> mybatis插件，[破解下载](https://github.com/pengzhile/MyBatisCodeHelper-Pro-Crack/releases/tag/v2.0.2)，赶紧体验吧！

## 9.7 .ignore

> git提交文件。

## 9.8 Gitee、Gitlab、GitHub

> git代码托管。

## 9.9 对象赋值

> 一键根据json文本生成java类 非常方便；一键调用一个对象的所有set方法并且赋予默认值 在对象字段多的时候非常方便。
>
> GsonFormat、GenerateAllSetter

## 9.10 脚本语言

> shell脚本开发和bat脚本，2019后的idea虽然自带的也，但是不好用。
>
> 推荐安装：Batch Scripts Supports（执行bat脚本）、BashSupport pro（执行shell脚本，需要禁用自带的shell）

## 9.11 Stackoverflow

> 问题搜索。

## 9.12 Grep Console

> 日志管理。

## 9.13 Background Image Plus

> 背景图片设置

## 9.13 Alibaba Cloud Toolkit

> Cloud Toolkit 帮助开发者将本地应用程序一键部署到线下自有 VM，或阿里云 ECS、EDAS 和 Kubernetes 中去。

## 9.14 SVN Disconnect

> 解除SVN的项目关联。

## 9.15 VisualVM Launcher

JVM虚拟机信息！！！

## 9.15 CodeGlance

便于代码的拖拽查看！！！

## 9.16 IdeaJad

Jad反编译工具！！！

## 9.17 Statistic

代码量统计！！！

1、打开IDEA 菜单 View

2、选择 Tool window

3、点击 Statistic

4、可以看到不同类型文件的统计

5、refresh 重新统计

6、上面的tab 切换可以查看不同类型文件的统计

# 10 Debug调试

## 10.1 基本调试

编程离不开调试。

## 10.2 条件调试

### 10.2.1 条件断点

循环中经常用到这个技巧，比如：遍历1个大List的过程中，想让断点停在某个特定值。在断点的位置，右击断点旁边的小红点，会出来一个界面，在Condition这里填入断点条件即可，这样调试时，就会自动停在i=10的位置。

### 10.2.2 回到上一步

该技巧最适合特别复杂的方法套方法的场景，好不容易跑起来，一不小心手一抖，断点过去了，想回过头看看刚才的变量值，如果不知道该技巧，只能再跑一遍。method1方法调用method2，当前断点的位置j=100，点击上图红色箭头位置的Drop Frame图标后，时间穿越了。

注：好奇心是人类进步的阶梯，如果想知道为啥这个功能叫Drop Frame，而不是类似Back To Previous 之类的，可以去翻翻JVM的书，JVM内部以栈帧为单位保存线程的运行状态，drop frame即扔掉当前运行的栈帧，这样当前“指针”的位置，就自然到了上一帧的位置。

## 10.3 多线程调试

多线程同时运行时，谁先执行，谁后执行，完全是看CPU心情的，无法控制先后，运行时可能没什么问题，但是调试时就比较麻烦了，最明显的就是断点乱跳，一会儿停这个线程，一会儿停在另一个线程。

## 10.4 多进程调试



## 10.5 远程调试

这也是一个装B的利器，本机不用启动项目，只要有源代码，可以在本机直接远程调试服务器上的代码，打开姿势如下：项目启动时，先允许远程调试

```properties
java -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8081 -jar demo.jar
```

起作用的就是

```properties
-Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=9081
```

注意：远程调试从技术上讲，就是在本机与远程建立scoket通讯，所以端口不要冲突，而且本机要允许访问远程端口，另外这一段参数，放要在`-jar` 或 `${main_class}`的前面

idea中设置远程调试前提是本机有项目的源代码 ，在需要的地方打个断点，然后访问一个远程的url试试，断点就会停下来。



# 11 认识STS

## 11.1 下载配置

[下载STS](https://spring.io/tools)软件

# 12 全局配置

## 12.1 统一项目编码

1、 workspace

![Workspace编码](http://upload-images.jianshu.io/upload_images/8185387-e2f8f17905df507d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、spelling

![Spelling](http://upload-images.jianshu.io/upload_images/8185387-d096eff491423615.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、web JSP Files

![JSP等文件](http://upload-images.jianshu.io/upload_images/8185387-119af52e3ee62b83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、content types => Java Properties File

![配置属性编码](http://upload-images.jianshu.io/upload_images/8185387-b30384c00e75887e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 12.2 统一代码格式

1、搜索form

![统一代码格式](http://upload-images.jianshu.io/upload_images/8185387-cbf7cf2aff9d9b34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 12.3 统一项目视图

> 切换到自己喜爱且方便的视图模式写，看个人情况。本次我们主要介绍两种视图，一种是Java包视图（package explorer），另一种是Java的项目视图（project explorer）。

### 12.3.1 package explorer

package explorer一般会在写Java简单项目和JavaWeb项目使用。

<img src="../插图/sts02.png" />

### 12.3.2 project explorer

project explorer一般会在写maven的聚合项目使用，便于查看项目直接的相互依赖关系。

<img src="../插图/sts01.png" />

## 12.4 错误和警告

### 12.4.1 忽略dubbo的错误信息

<img src="../插图/jQuery.png" />

### 12.4.1 忽略JQuery.min.js

打开项目下的.project文件夹,把以下内容注释！![注释内容](http://upload-images.jianshu.io/upload_images/8185387-42b599efdb90e066.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 12.5 Tomcat的配置

### 12.5.1 Tomcat服务调优

关于TOMCAT端口的一些事情

一、Tomcat有三个端口号
​        1. 8080 ---- 服务访问端口
​        2. 8005 ---- 8005是远程关闭tomcat
​        3. 8009 ---- Apache代理时用的

二、Tomcat的优化配置

```xml
  <Server port ="800 5" shutdown ="SHUTDOWN">
                改为
  <Server port ="- 1" shutdown ="SHUTDOWN" >

  <Connector port="8080" protocol ="HTTP/ 1.1"  conne ctionT imeout ="20000"  redirectPort="8443" useBodyEncodingForURI="true" URIEncoding="UTF-8" />

  <Connector port="8009" protocol="AJP/ 1.3" redirectPort="8443" />本行删除，这样做原因是端口监听的多，Tomcat服务器越慢！
```


### 12.5.2 Tomcat的配置

![图-07](http://upload-images.jianshu.io/upload_images/8185387-8ac9c587d8046cab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图-08](http://upload-images.jianshu.io/upload_images/8185387-3003fb065692d980.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图-09](http://upload-images.jianshu.io/upload_images/8185387-3f268d5099bcdbd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图-10](http://upload-images.jianshu.io/upload_images/8185387-1c14c5ea6601e848.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图-11](http://upload-images.jianshu.io/upload_images/8185387-14f84c06bf55f455.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图-12](http://upload-images.jianshu.io/upload_images/8185387-7bac0605c6455f34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 12.6 快捷键

### 12.6.1 常用快捷键

```properties
Alt+/：代码提示
Ctrl+Alt+下：复制当前行代码
ctrl+shift+r：打开资源
ctrl+o：快速outline
Ctrl+1：快速修复(最经典的快捷键,就不用多说了)
ctrl+2：L：为本地变量赋值
ctrl+6：快速定位controller
Alt+Shift+R：快速修改所有的变量名
ctrl + shift+o：搜索一个类中的方法
ctrl + o：搜索一个类中的方法
ctrl + h 打开搜索
alt + F5 更新项目（当项目没有错误，但是仍然出现错误标记时使用）
Ctrl+D: 删除当前行
Ctrl+T：快速显示当前类的继承结构
Ctrl+/：注释当前行,再按则取消注释
Ctrl+Shift+F：格式化当前代码
Alt+shift+S：书写实体类是时候使用（C+O+R+S）
Alt+F5：校对maven的jar包
ctrl + shift + x：大写
ctrl + shift + y：小写
向左：将要移动的代码选中，然后按TAB键
向右：将要移动的代码选中，然后按shift+tab键
更多快捷键组合可在Eclipse按下ctrl+shift+L查看。
调试中：
F5      进入执行的方法中
F6      当前代码的表面执行，单行执行下一步
F7      不再观察，返回进入处
F8      停止调试，直接正常执行完毕 
```

### 12.6.2 输入@给提示

![输入@自动提示](http://upload-images.jianshu.io/upload_images/8185387-46a6786d0609e9ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 12.6.3 Eclipse.ini文件的配置

```properties
-startup
plugins/org.eclipse.equinox.launcher_1.3.200.v20160318-1642.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_64_1.1.400.v20160518-1444
-product
org.eclipse.epp.package.jee.product
--launcher.defaultAction
openFile
-showsplash
org.eclipse.platform
--launcher.defaultAction
openFile
--launcher.appendVmargs
// 配置自己的jdk文件路径
-VM
E:\dev\soft\jdk8\bin\javaw.exe
-vmargs
-Dosgi.requiredJavaVersion=1.8
-XX:+UseG1GC
-XX:+UseStringDeduplication
-Dosgi.requiredJavaVersion=1.8
-Xms256m
-Xmx1024m
```



## 12.7 代码注释配置

### 12.7.1 类注释

```java
/**
 * project - ETC发票系统
 *
 * @author ${user} 
 * @date 日期:${date} 时间:${time}
 * @JDK 1.8 
 * @version 1.0
 * @since 1.0
 * @Description 功能模块： 
 */
```

### 12.7.2 方法注释

```java
 /**
 * @Title ${enclosing_method}  
 * ${tags} 
 * @date 日期:${date} 时间:${time}
 * @return ${return_type}
 * @Description 功能： 
 */
```

# 13 STS版本控制

## 4.1 SVN项目提交

### 13.1配置SVN

**步骤一：提交项目到SVN**
<img src="../插图/sts-svn.png" />

### 4.1.2 项目提交



### 4.1.3 项目检出

**步骤一：从SVN下载项目代码**

<img src="../插图/sts-svn-download.png" />

**步骤二：将项目代码转成maven项目**

<img src="../插图/sts-update.png" />

### 4.1.4 项目打分支



### 4.1.5 项目分支合并



### 4.1.6 项目Tag标签



## 4.2 Git使用

> eclipse和sts都内置有git插件。

### 4.2.1 配置Git



### 4.2.2 提交项目



### 4.2.3 检出项目



### 4.2.4 项目打分支



### 4.2.5 项目合并



### 4.2.6 项目Tag标签



# 14 STS项目构建

## 13.1 Gradle



## 13.2 Maven



## 13.3 Boot项目



# 15 STS插件配置

### 6.1 lombok

> 代码简洁工具包。


