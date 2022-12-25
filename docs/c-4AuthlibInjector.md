------

# Authlib Injector

> 作者：SRY_CTB

- 本插件只是基于 Blessing Skin 皮肤站原有的用户系统，完整实现了一套符合 Yggdrasil API 规范的 API（这一套规范就是 Mojang 官方用于 Minecraft 正版登录鉴权的 API），而我们必须使用 authlib-injector 这个程序，以在运行时将游戏内的「Mojang 正版登录的 API 地址」替换成「皮肤站提供的 Yggdrasil API 地址」，从而实现外置登录系统。
### 配置启动器
如果您或您所使用的皮肤站安装有「正版验证」插件，并且您的账号经过了正版验证，则可以使用 任意一款启动器（只需要允许自定义 JVM 参数），然后在启动器的设置中修改 JVM 参数以使游戏客户端加载 authlib-injector 即可。
#### HMCL

#### MCCL

### 服务端加载 authlib-injector
>使用本外置登录方案，您必须在 server.properties 中将 online-mode 设置为 true。
>
>如果您正在使用 BungeeCord，那么在所有服务端上都需要加载 authlib-injector，但应只有 BungeeCord 打开 online-mode，其它服务端应关闭 online-mode。

用于启动 Minecraft 服务端的脚本一般来说是长这样的（使用 .bat 文件的用户请使用文本编辑器打开修改。您的启动命令可能与示例有些不同，这是正常的。在您自己的启动命令中能找到类似 -jar xxx.jar 的部分即可）：
<br>
``` java java -Xmx1024M -Xms1024M -jar minecraft_server.1.12.2.jar nogui ```