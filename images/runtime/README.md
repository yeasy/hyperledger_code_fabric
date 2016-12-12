## runtime

生成一个简单的运行时镜像。

包括 Dockerfile.in 文件，内容为：

```sh
FROM scratch
COPY payload/busybox /bin/busybox
RUN ["/bin/busybox", "mkdir", "-p", "/usr/bin", "/sbin", "/usr/sbin"]
RUN ["/bin/busybox", "--install"]
RUN mkdir -p /usr/local/bin
ENV PATH=$PATH:/usr/local/bin
```