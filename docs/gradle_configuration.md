## Android Studio中Gradle配置详解

2018/11/13

### 前言

在着手配置前，我们先简单了解下面这三个玩意：

`Gradle`是一款开源的自动化构建工具，但不是Android的专属构建工具。

`Gradle Wrapper`相当于Gradle管理器，它记录了当前项目所依赖的Gradle版本，并能自动完成安装、部署工作。（方便管理多个项目）

`Android Gradle plugin`是Google团队主导开发的适合Android开发的Gradle插件。

### Gradle

首先要知道当前项目所依赖的版本，查看build.gradle文件：

    task wrapper(type: Wrapper) {
      gradleVersion = '4.4.1'
    }
    
然后下载对应的[gradle](http://services.gradle.org/distributions/)`.zip`文件。

### Gradle Wrapper

用studio新建的项目自带wrapper或者导入gradle项目时会提示是否生成wrapper，推荐选`是`，就会自动生成一个`gradle/wrapper`目录，里面有两个文件：gradle-wrapper.jar、gradle-wrapper.properties。其中gradle-wrapper.jar是Gradle Wrapper的主体功能包，在Android Studio安装过程中产生（如果默认安装的话会在`..\Android Studio\plugins\android\lib\templates\gradle\wrapper\gradle\wrapper\gradle-wrapper.jar`）。然后每次新建项目，会将gradle-wrapper.jar拷贝到你的项目的gradle/wrapper目录中。gradle-wrapper.properties文件主要指定了该项目需要什么版本的Gradle，从哪里下载该版本的Gradle，下载下来放到哪里。

    distributionBase=GRADLE_USER_HOME
    distributionPath=wrapper/dists
    zipStoreBase=GRADLE_USER_HOME
    zipStorePath=wrapper/dists
    distributionUrl=https\://services.gradle.org/distributions/gradle-4.4.1-all.zip

注：这时候gradle-wrapper.properties文件中的gradle版本号可以和你项目依赖的对不上，需要手动修改。

其中GRADLE_USER_HOME一般指`~/.gradle`目录。Gradle Wrapper会把`gradle-4.4.1`下载到本地的`~/.gradle/wrapper/dists`对应gradle版本的目录并解压。

注：如果网速不理想的情况下，就需要参考第一部分的介绍手动下载`gradle-4.4.1-all.zip`文件，接着进入对应的gradle版本目录下，会发现有一个一串乱码的目录，将压缩包复制到一串乱码的目录下，删除未下载完的`.part`文件，重启项目即可。

然后是Android Studio中的配置`setting->Build->Gradle`

    Use default gradle wrapper(recommended) // 推荐勾选此项，每个项目对应一个wrapper
    Use local gradle distribution // 理论上可以所有的项目共用一个Gradle，但事实上兼容性是个大问题，所以不推荐

### Android Gradle plugin

继续看build.gradle文件

    buildscript {
        repositories {
          jcenter()
          google()
        }
        dependencies {
          classpath 'com.android.tools.build:gradle:3.1.4'
        }
    }

看到这里可以发现原来Gradle版本和Android Gradle plugin插件版本是不一致的，可以在Android官网上查看gradle版本和[gradle-plugin](https://developer.android.google.cn/studio/releases/gradle-plugin)版本的对应关系。

### 科学上网

继续看build.gradle文件

    buildscript {
      repositories {
        // jcenter()
        // google()
        
        /* 阿里镜像 */
        maven { url 'https://maven.aliyun.com/repository/public' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'https://maven.aliyun.com/repository/google' }
      }
      dependencies {
        classpath 'com.android.tools.build:gradle:3.1.4'
      }
    }

从注释掉的部分可以看出插件是从jcenter和google处下载的，它会下载到`~/.gradle/caches/modules-2/files-2.1/com.android.tools.build`目录。

注1：同上，网速不理想的时候可以手动下载插件到指定目录。（需要在Android Studio中配置`setting->Build->Gradle`勾选`Offline work`）

注2：更推荐使用阿里的镜像，使用方法见build.gradle文件中注释的部分。
