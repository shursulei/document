shell获取某个目录下的文件的绝对名称

```
find  $PWD | xargs ls -ld | grep ^- | awk -F' ' '{print $9}'
```

端口转发

```shell
ssh root@121.199.52.252 -L 3306:localhost:3306
ssh root@121.199.52.252 -L 8694:localhost:8694
ssh root@121.199.52.252 -L 8080:localhost:8080
ssh root@121.199.52.252 -L 4040:localhost:4040
```

```
df -hl 查看磁盘剩余空间
df -h 查看每个根路径的分区大小
du -sh [目录名] 返回该目录的大小
du -sm [文件夹] 返回该文件夹总M数
du -h [目录名] 查看指定文件夹下的所有文件大小（包含子文件夹）
```

