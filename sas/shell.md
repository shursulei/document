```shell
awk 'END {print}'#取最后一行
echo "$STR" | grep -o "[^ ]\+\( \+[^ ]\+\)*" #trim的功能
ls -ltr
ls -lt


```

判断字符串是否是空值

```shell
#!/bin/sh
STRING=
if [ -z "$STRING" ]; then
    echo "STRING is empty"
fi
if [ -n "$STRING" ]; then
    echo "STRING is not empty"
fi
```

查询指定时间范围内的日志

```shell
grep  成功  sed -n '/2018-09-06 16:00:00/,/2018-09-06 17:00:00/p' all.2018-09-06.log
```

