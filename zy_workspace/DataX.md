# DataX趟过的坑

python版本:2.6.x版本之上

datax版本默认的mysql支持的是5.x的版本，对mysql8.x的版本的兼容性存在问题，需要替换mysql的连接jar包

将datax\plugin\reader\mysqlreader的mysql-connector-java-5.1.34.jar换成mysql-connector-java-8.0.18.jar，同理mysqlWriter

> 报错:datax 无法链接mysql:MySQLNonTransientConnectionException

json中必须设置job

```json
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
```



> com.alibaba.datax.common.exception.DataXException:  - Job运行速度必须设置



datax数据同步

```
python datax.py /data/mysql-datax.json
```



# 全量更新

# 增量更新