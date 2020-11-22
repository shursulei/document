快照<http://www.cnblogs.com/nucdy/p/5684196.html>

hdfs dfsadmin -allowSnapshot /user_c

hdfs lsSnapshottableDir

hadoop fs -ls  /user_c/.snapshot

hadoop fs -ls -R /user_c/.snapshot

hdfs snapshotDiff /user_c  snapshot0 snapshot1

hdfs dfs -createSnapshot /user_c snapshot1

快照和回滚升级一起
