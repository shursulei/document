一、Hive下查看数据表信息的方法

方法1：查看表的字段信息

desc table_name;

方法2：查看表的字段信息及元数据存储路径

desc extended table_name;

方法3：查看表的字段信息及元数据存储路径

desc formatted table_name;

备注：查看表元数据存储路径是，推荐方法3，信息比较清晰。