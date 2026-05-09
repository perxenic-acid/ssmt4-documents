## 介绍

对于 异环 这个游戏来说，最好的方式是使用.pak格式的UE引擎重打包Mod，避免使用3Dmigoto所带来的一系列问题


## ssice-a的3Dmigoto适配
Github主页：

https://github.com/ssice-a

NTMI-Package

https://github.com/ssice-a/NTMI-PACKAGE

NTMI专属d3d11.dll

https://github.com/ssice-a/XXMI-Libs-Package

NTMI-Tools (导入导出Blender插件)

https://github.com/ssice-a/mod_importer


## SSMT4使用异环Mod

目前SSMT4已添加了ssice-a开发的NTMI专属d3d11.dll的自动下载支持，以及NTMI-Package的自动更新支持

但是我个人觉得使用pak重打包的方式会更好一些，所以暂时不会添加模型提取和Mod生成逻辑

配置方式如下：

游戏预设：NTEMI

目标进程路径：C:\Program Files\Neverness To Everness\Client\WindowsNoEditor\HT\Binaries\Win64\HTGame.exe

启动器路径：空着不填

启动参数：空着不填

点击开始游戏自动启动Run.exe，然后手动从官方启动器启动游戏即可，官方启动器中要设为dx11模式

