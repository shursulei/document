最近使用Java调用本地命令unar unrar时出现process.waitFor()卡住不运行问题，特此记录

```
Process proc = Runtime.getRuntime().exec(cmd);
```

如果代码中不指定InputStream、ErrorStream，默认是会写入本地操作系统的缓冲区，这个缓冲区空间有限因为压缩包中有大量的文件解压过程中输出的信息很多，缓冲区满了，或错误信息过多占满缓冲区，就会产生卡住的问题

解决办法 指定标准错误、和标准输入到我们的程序而不是缓冲区 proc.getErrorStream() proc.getInputStream()

> 引用一段JDK Class Process文档的描述 Runtime.exec方法创建过程可能不是某些本地平台特殊工序的操作，如本地的窗口过程，守护进程，运行在微软Windows、DOS程序，或shell脚本。 默认情况下，创建的子进程没有自己的终端或控制台。所有标准I / O（即stdin，stdout和stderr）操作将被重定向到父进程，在那里他们可以通过流获得的使用方法getOutputStream()，getInputStream()访问，和getErrorStream()。父进程使用这些流给输入和输出的过程。因为一些本地平台仅提供了标准的输入和输出流有限的缓冲区大小，未能及时写或读输入流的子进程的输出流可能会造成子进程的阻塞，甚至死锁。

```java
Process process = Runtime.getRuntime.exec(command); // 调用外部程序
final InputStream is1 = process.getInputStream();
new Thread(new Runnable() {
    public void run() {
        BufferedReader br = new Buffered(new InputStreamReader(is)); 
        while(br.readLine() != null) ;
    }
}.start(); // 启动单独的线程来清空process.getInputStream()的缓冲区
InputStream is2 = process.getErrorStream();
BufferedReader br2 = new Buffered(new InputStreamReader(is2)); 
StringBuilder buf = new StringBuilder(); // 保存输出结果流
String line = null;
while((line = br.readLine()) != null) buf.append(line); // 循环等待ffmpeg进程结束
System.out.println("输出结果为：" + buf);
```

