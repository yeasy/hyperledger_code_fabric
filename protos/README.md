# protos
Protobuf 格式的数据结构和消息协议。都在同一个 protos 包内。

这里面是所有基本的数据结构（message）定义和 GRPC 的服务（service）接口声明。

所有的 `.proto` 文件是 protobuf 格式的声明文件，`.pb.go` 文件是基于 `.proto` 文件生成的 go 语言的类文件。

protobuf 工具可以从 [这里](https://developers.google.com/protocol-buffers/docs/downloads) 下载，推荐使用 3.0 版本系列。

下载安装后，需要安装对应语言的编译器，例如要生成 go 语言代码，则需要安装 `protoc-gen-go`。

```sh
$ go get github.com/golang/protobuf/protoc-gen-go
```

可以使用 protobuf 编译器基于 protobuf 模板文件来生成各种语言的类文件。

```sh
$ protoc \
--proto_path=IMPORT_PATH \
--cpp_out=DST_DIR \
--java_out=DST_DIR \
--python_out=DST_DIR \
--go_out=DST_DIR \
--ruby_out=DST_DIR \
--javanano_out=DST_DIR \
--objc_out=DST_DIR \
--csharp_out=DST_DIR \
path/to/file.proto
```

其中，`--proto_path=IMPORT_PATH` 是当 proto 文件中存在导入时候，进行查找的路径，等价于 `-I=IMPORT_PATH`，可以多次使用来指定多个导入路径。

为了生成支持 grpc 的代码，还可以提供生成参数 `plugins=grpc`，例如

```sh
$ protoc \
--proto_path=IMPORT_PATH \
--go_out=plugins=grpc:DST_DIR \ 
path/to/file.proto
```

另外，生成的结构体，一般都至少默认支持 4 个默认生成的方法。

* Reset()：重置结构体。
* String() string：返回代表对象的字符串。
* ProtoMessage()：协议消息。
* Descriptor([]byte, []int)：描述信息。


