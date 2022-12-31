------

# Authlib Injector

> 作者：SRY_CTB

- 本插件只是基于 Blessing Skin 皮肤站原有的用户系统，完整实现了一套符合 Yggdrasil API 规范的 API（这一套规范就是 Mojang 官方用于 Minecraft 正版登录鉴权的 API），而我们必须使用 authlib-injector 这个程序，以在运行时将游戏内的「Mojang 正版登录的 API 地址」替换成「皮肤站提供的 Yggdrasil API 地址」，从而实现外置登录系统。
### 配置启动器
如果您或您所使用的皮肤站安装有「正版验证」插件，并且您的账号经过了正版验证，则可以使用 任意一款启动器（只需要允许自定义 JVM 参数），然后在启动器的设置中修改 JVM 参数以使游戏客户端加载 authlib-injector 即可。

>详情请见 [Yggdrasil API 认证服务器](https://docs.starskin.eu.org/#/./docs/c-3YggdrasilAPI%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8)
### 服务端加载 authlib-injector

>使用本外置登录方案，您必须在 server.properties 中将 online-mode 设置为 true。
>
>如果您正在使用 BungeeCord，那么在所有服务端上都需要加载 authlib-injector，但应只有 BungeeCord 打开 online-mode，其它服务端应关闭 online-mode。

用于启动 Minecraft 服务端的脚本一般来说是长这样的（使用 .bat 文件的用户请使用文本编辑器打开修改。您的启动命令可能与示例有些不同，这是正常的。在您自己的启动命令中能找到类似 -jar xxx.jar 的部分即可）：


``` java java -Xmx1024M -Xms1024M -jar minecraft_server.1.12.2.jar nogui ```

接下来请在启动命令中 正确的位置（-jar xxx.jar 之前）插入 -javaagent (opens new window)参数。

假设：
 - 您下载到了文件名为 authlib-injector.jar 的文件
 - 并将其放到了与服务端核心 minecraft_server.jar 相同的目录下
 - 您的 Yggdrasil 服务器 API 为 https://star-skin.shanrenyi.top/api/yggdrasil

那么您应该将上面的示例启动命令修改为这样：

``` java -Xmx1024M -Xms1024M -javaagent:authlib-injector.jar=https://star-skin.shanrenyi.top/api/yggdrasil -jar minecraft_server.jar nogui ```

### 正确加载 authlib-injector
需要注意的是，authlib-injector 是通过 JVM 的 -javaagent 参数来实现运行时字节码替换的，所以您必须保证：
 - 启动器支持使用自定义 Yggdrasil API 进行认证
 - Minecraft 游戏本体正确加载了 authlib-injector
 - Minecraft 服务端加载了 authlib-injector（必须 online-mode=true）
 - 以上三者必须使用同一个 Yggdrasil API
 
否则会出现：
 
 - 无法登录
 - 不显示皮肤或显示错乱
 - 无法进入服务器
 - 等等情况
 
### 成功喽 (❤ ω ❤)
<br>
<br>
<div style="text-align: center;">
<img src=https://img-2.shanrenyi.top/i/2022/07/12/62cd96fb0d17a.png width=100% alt="StarSkin"/>
<img src=https://img-2.shanrenyi.top/i/2022/07/12/62cd96f98eacd.png width=100% alt="StarSkin"/>
<img src=https://img-2.shanrenyi.top/i/2022/07/12/62cd96f75b594.png width=100% alt="StarSkin"/>
</div>

### 错误排查
#### 您所使用的皮肤站插件是否是最新版本
很有可能您遇到的问题已经在最新版本中修复过了。同理，请检查您的启动器是否是最新版本。

#### 启动器是否配置正确
如果您无法正常登录并启动游戏，请参阅 [Yggdrasil API 认证服务器](https://docs.starskin.eu.org/#/./docs/c-3YggdrasilAPI%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8) 检查您的配置。

#### 游戏本体是否正确加载 authlib-injector
如果您正在使用 HMCL3，那么为 Minecraft 游戏本体加载 authlib-injector 的工作是由启动器自动完成的，所以只要您正确配置了 HMCL3，此步骤一般不会出现问题。

如果您正在使用其他启动器，请您打开该启动器的「查看日志 / 显示日志」选项后再重新启动游戏（.minecraft/logs/latest.log 中是不会有这部分的日志的，还请注意），观察启动日志中是否出现如下字样：

```java
[authlib-injector] launched from premain
[authlib-injector] api root: https://star-skin.shanrenyi.top/api/yggdrasil
[authlib-injector] fetching metadata
[authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/profile/] to [https://star-skin.shanrenyi.top/api/yggdrasil/sessionserver/session/minecraft/profile/]
[authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/join] to [https://star-skin.shanrenyi.top/api/yggdrasil/sessionserver/session/minecraft/join]
[authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/hasJoined] to [https://star-skin.shanrenyi.top/api/yggdrasil/sessionserver/session/minecraft/hasJoined]
```

如果有，就说明您已经正确加载了 authlib-injector。如果没有，请重新配置您启动器中的 JVM 参数。

<div style="text-align: center;">
<img src=https://img-2.shanrenyi.top/i/2022/07/12/62cd997e04690.png width=100% alt="StarSkin"/>
</div>

#### 服务端是否配置正确
如果服务端正确加载了 authlib-injector 的话，您应该可以在控制台看到如下输出：

```java
/[authlib-injector] launched from premain
[authlib-injector] api root: https://skin.test/api/yggdrasil
[authlib-injector] fetching metadata
[22:56:36] [main/INFO]: [STDERR]: [authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/profile/] to [https://skin.test/api/yggdrasil/sessionserver/session/minecraft/profile/]
[22:56:36] [main/INFO]: [STDERR]: [authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/join] to [https://skin.test/api/yggdrasil/sessionserver/session/minecraft/join]
[22:56:36] [main/INFO]: [STDERR]: [authlib-injector] transform [https://sessionserver.mojang.com/session/minecraft/hasJoined] to [https://skin.test/api/yggdrasil/sessionserver/session/minecraft/hasJoined]
```

如果没有，请参照 [Authlib Injector](https://docs.starskin.eu.org/#/./docs/c-4AuthlibInjector) 重新进行配置。这时候您应该可以进入服务器，看到自己和别人的皮肤正确加载。如果您无法进入服务器，请继续往下看。

#### 常见问题
##### 皮肤无法加载
如果您能够进入游戏却无法加载皮肤的话，请检查：
 - 客户端到皮肤站的网络是否畅通【玩家】
>一般情况下，客户端日志中也会有相应的报错，请查阅以寻找可能的原因。
>
>注意，此方法不支持「高清皮肤 / 披风」，如确实有需要，请使用 CSL 等皮肤 Mod。

##### 无法进入服务器
一般来说这说明您游戏客户端、游戏服务端或者皮肤站的 Yggdrasil API 出现了问题。为了快速定位问题、减少排查所需的时间，您可以使用我们提供的[Yggdrasil API 认证服务器](https://docs.starskin.eu.org/#/./docs/c-3YggdrasilAPI%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8)来确认您遇到的问题是出现在启动器、游戏客户端，检查服务端见[Authlib Injector](https://docs.starskin.eu.org/#/./docs/c-4AuthlibInjector)

>如果您将原本皮肤站的 API Root 修改为上面提供的「 https://star-skin.shanrenyi.top/api/yggdrasil」后可以正常进入游戏、加载皮肤、加入服务器的话，那就说明您遇到的问题出在 皮肤站 上，请联系我们(Arenjunsanyan@126.com)排查错误。如果使用了样例 API 后问题依然存在，即说明您的问题是启动器、客户端或是游戏服务端的错误配置导致的，请参照本docs的配置教程重新进行相关的配置。

### 报告问题的正确姿势
>任何事物都不可能永远尽善尽美，Star Skin 也是如此。 因此，当你在使用 Star Skin 的途中遇到问题时（包括但不限于：你自己无法解决的报错、BUG、安全漏洞），不要气急败坏，请遵守下述规则向开发者提交错误报告。

首先，你应该确定这到底是不是 StarSkin 的锅：机器宕机、网络问题、甚至是你的浏览器出错都会导致你无法正常使用。因此，在向开发者报告问题之前，你应该先以自己的经验判断一下，这到底是不是StarSkin 的锅。如果你自己判断不了，就在互联网上搜索一下吧，Google ,Baidu and SouGou are your friend !!!

<div style="text-align: center;">
<img src=https://img-2.shanrenyi.top/i/2022/12/26/63a91c796490b.jpg width=20% alt="StarSkin"/>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 
<img src=https://img-2.shanrenyi.top/i/2022/12/26/63a91ce4c68b6.jpg width=10% alt="StarSkin"/>
</div>

其次，你应该好好阅读一遍 常见问题解答，并确保你的问题不在此列。很多情况下你遇到的问题也曾经坑过不少人，并且他们已经总结出了解决方法：那你为什么不看一看呢？
确认你遇到的问题不在其中后，你可以着手准备错误报告了。

#### 我应该提供什么信息给开发者以帮助调试？
 1. 报错截图<br>
 如果是程序的问题，应该会有错误信息提示，请把截图发。
 2. 日志<br>
 如果是游戏内皮肤无法显示等问题，你需要带上你的皮肤 Mod 日志。例如，CustomSkinLoader 的日志（位于 .minecraft/CustomSkinLoader/CustomSkinLoader.log）。
 3. 如果是 Yggdrasil 外置登录的问题
 - 游戏服务器从启动开始的所有日志
 - 游戏客户端从启动开始的所有日志
 4. 游戏客户端从启动开始的所有日志
 - 你用什么浏览器访问程序时出现了问题？
 - 出现问题时浏览器地址栏上的 URL 是什么？
 - 等等。。。。