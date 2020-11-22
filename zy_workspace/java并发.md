java并发的挑战

上下文切换(CPU)

死锁

资源限制

```
jstack 7896 > 1.txt
grep java.lang.Thread.State 1.txt | awk '{print $2$3$4$5}' |sort | uniq -c
```

