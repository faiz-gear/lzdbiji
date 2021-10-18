---
title: h5与ue4通讯
tag: h5与ue4
keywords: h5与ue4通讯 js与h5交互 web与h5通讯
categories: h5与ue4
---
## 一、技术方案：

UE4通过本地路径导入前端打包的项目，引入对应的h5页面，借助UE4的WebBrowser插件实现与h5的双向通信

注：本文档以纵横项目demo为例，主要介绍前端需要做的相关工作

## 二、前端技术栈

vue3+quasar+axios+vue-router

注：使用什么技术栈并不影响与ue的交互，但是后续在引入项目中会存在不兼容的问题，后续打包时需要配置进行polyfill处理，详见问题及解决方案1

## 三、核心流程

<!--ue4传数据给h5后续补充-->

1. h5传数据给UE4

   - 判断h5中全局环境下是否已经有ue对象（UE4中WebBrowser插件不同版本或者不同插件，可能拿到的是ue4对象，当前项目拿到的是ue对象）

    ```js
     const ue = window.ue;
     const isHasUE = typeof ue !== "undefined" && ue !== null ? true : false;
     ```

   - 调用UE中定义的挂载在ue对象中的对应方法

    ```js
     function MinimizeApp(trackdata) {
       return function(callback) {
         if (!isHasUE) {
           console.log("ue initial faild! can't call function!~");
           return;
         }
          // 这里的webapi是和ue同事沟通统一的名称 
         ue.webapi
         	// 这里的方法名要全部小写，需要和ue同事进行统一
           .minimizeapp(JSON.stringify(trackdata))
           .then(function(ret) {
             // ret是调用ue的返回值。UE4中WebBrowser插件不同版本或者不同插件，可能返回值名称必须要是ReturnValue
             callback(ret);
           })
           .catch(function(error) {
             console.log(error);
           });
       };
     }
     ```

     

## 四、问题及解决方案

1. UE4加载web项目显示空白问题

   - 问题产生原因：UE4中的Chromium内核不支持ES module

   - UE4自带的WebBrowser使用CEF3（使用Chromium内核）, 对应的内核版本信息如下：

    ```
     CEF 3.3071.1611.g4a19305
     Chromium    59.0.3071.15
     OS  Windows
     WebKit  537.36 (@b3f9fb3b948d9304d587a127c3e4f47b2ad78927)
     JavaScript  5.9.211.4
     Flash   
     User Agent  Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Digital/++UE4+Release-4.26-CL-15973114 UnrealEngine/4.26.2-15973114+++UE4+Release-4.26 Chrome/59.0.3071.15 Safari/537.36
     Command Line    "D:/Program Files/Epic Games/UE_4.26/Engine/Binaries/Win64/UE4Editor.exe" --browser-subprocess-path="D:/Program Files/Epic Games/UE_4.26/Engine/Binaries/Win64/UnrealCEFSubProcess.exe" --no-sandbox --product-version="Digital/++UE4+Release-4.26-CL-15973114 UnrealEngine/4.26.2-15973114+++UE4+Release-4.26 Chrome/59.0.3071.15" --lang=en --log-file=D:/Projects/digital-factory/Digital/Saved/Logs/cef3.log --log-severity=warning --resources-dir-path="D:/Program Files/Epic Games/UE_4.26/Engine/Binaries/ThirdParty/CEF3/Win64/Resources" --locales-dir-path="D:/Program Files/Epic Games/UE_4.26/Engine/Binaries/ThirdParty/CEF3/Win64/Resources/locales" --enable-gpu --enable-gpu-compositing --enable-begin-frame-scheduling
     Module Path D:\Program Files\Epic Games\UE_4.26\Engine\Binaries\ThirdParty\CEF3\Win64\libcef.dll
     Cache Path  D:/***/Saved/webcache
     ```

   - 通过查询该浏览器版本支持的对应功能（查询网站：[查询浏览器支持的语法、API等](http://html5test.com/))发现，该浏览器不支持ES module语法，需要对项目进行polyfill打补丁，让UE4支持项目中一些新的API。

   - ![cef3-htm5test](/Users/luyao/Desktop/wattman/zongheng/cef3-htm5test.png)

   - 解决方案：vue-cli4中会默认这样它会根据源代码中出现的语言特性自动检测需要的 polyfill。这确保了最终包里 polyfill 数量的最小化。然而，这也意味着**如果其中一个依赖需要特殊的 polyfill，默认情况下 Babel 无法将其检测出来。**使用vue-cli4中的transpileDependencies选项，对可能有造成不兼容问题的依赖进行polyfill。如项目中对quasar进行转换

    ```vue
     transpileDependencies: ['quasar' ]
     ```

2. h5给ue4传输数组数据或对象失败问题

   - 问题产生原因：与浏览器与后端进行数据传输类似，h5与ue进行数据传输也需要对数据进行序列化。由于平时使用axios进行网络数据请求，其内部对传输的数据就已经进行了序列化的操作，此处在与u4通信时，并不是通过axios，遗忘了对数据进行序列化的操作
   - 解决方案：使用浏览器的API对数据进行序列化操作后传给ue：JSON.stringify()