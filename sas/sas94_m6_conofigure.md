sas_94打开pipe和X COMMAND的

-allowxcmd/-noxmd



使用SMC 选择SASAPP ，选择SASAPP->logical Pooled Workspace Server->右击属性->选择选项->高级选项->选择启动属性->点击允许XCMD

然后重启objectSpawner服务

sas用户

{xx/sasconfig}/Levl/ObjectSpawner/

./ObjectSpawner stop

./ObjectSpawner start

