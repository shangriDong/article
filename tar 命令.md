```
[root@linux ~]# tar -cvf /tmp/etc.tar /etc<==仅打包，不压缩！ 
[root@linux ~]# tar -zcvf /tmp/etc.tar.gz /etc<==打包后，以 gzip 压缩 
[root@linux ~]# tar -jcvf /tmp/etc.tar.bz2 /etc<==打包后，以 bzip2 压缩 
```

> 特别注意，在参数 f 之后的文件档名是自己取的，我们习惯上都用 .tar 来作为辨识。 
> 如果加 z 参数，则以 .tar.gz 或 .tgz 来代表 gzip 压缩过的 tar file ～ 
> 如果加 j 参数，则以 .tar.bz2 来作为附档名啊～ 
> 上述指令在执行的时候，会显示一个警告讯息：

```
[root@linux src]# tar -zxvf /tmp/etc.tar.gz 
```

>  在预设的情况下，我们可以将压缩档在任何地方解开的！以这个范例来说， 
> 我先将工作目录变换到 /usr/local/src 底下，并且解开 /tmp/etc.tar.gz ， 
> 则解开的目录会在 /usr/local/src/etc 呢！另外，如果您进入 /usr/local/src/etc 
> 则会发现，该目录下的文件属性与 /etc/ 可能会有所不同喔！