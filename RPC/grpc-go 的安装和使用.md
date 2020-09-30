# grpc-go 的安装和使用

简单来讲 gRPC 是一个 基于 HTTP/2 协议设计的 RPC 框架，它采用了 Protobuf 作为 IDL (接口定义语言)

## grpc-go 安装

1. 使用 go get 安装代码库
```bash
go get -u google.golang.org/grpc
```
2. 安装 protocol buffers v3
mac os 下使用 homebrew 安装
```sh
  brew install automake
  brew install libtool
  brew install protobuf
```

3. 安装针对Go语言的代码生成插件
```sh
go get -u github.com/golang/protobuf/protoc-gen-go
```

4. 加入到PATH路径
```sh
export PATH=$PATH:$GOPATH/bin
```

5. 编写 .proto 文件
```sh
vim helloworld.proto
```
```go
syntax = "proto3";

package protos;
// The greeting service definition.
service Hello {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}

```

6. 编译 .proto 文件
```sh
protoc --go_out=plugins=grpc:. *.proto
//或
protoc --gofast_out=plugins=grpc:. *.proto

protoc -I hello/ hello/protos/hello.proto --go_out=plugins=grpc:hello
```



**官方 example 运行**

server.go 建立

```go
package main

import (
    "log"
    "net"

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    pb "go-server/app/rpc/hello/protos"
    "google.golang.org/grpc/reflection"
)

const (
    port = ":50051"
)

// server is used to implement helloworld.GreeterServer.
type server struct{}

// SayHello implements helloworld.GreeterServer
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
    return &pb.HelloReply{Message: "Hello " + in.Name}, nil
}

func main() {
    lis, err := net.Listen("tcp", port)
    if err != nil {
        log.Fatalf("failed to listen: %v", err)
    }
    s := grpc.NewServer()
    pb.RegisterHelloServer(s, &server{})
    // Register reflection service on gRPC server.
    reflection.Register(s)
    if err := s.Serve(lis); err != nil {
        log.Fatalf("failed to serve: %v", err)
    }
}
```
**client 建立**

```go
package main

import (
    "log"
    "os"

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    pb "go-server/app/rpc/hello/protos"
)

const (
    address     = "localhost:50051"
    defaultName = "world"
)

func main() {
    // Set up a connection to the server.
    conn, err := grpc.Dial(address, grpc.WithInsecure())
    if err != nil {
        log.Fatalf("did not connect: %v", err)
    }
    defer conn.Close()
    c := pb.NewHelloClient(conn)

    // Contact the server and print out its response.
    name := defaultName
    if len(os.Args) > 1 {
        name = os.Args[1]
    }
    r, err := c.SayHello(context.Background(), &pb.HelloRequest{Name: name})
    if err != nil {
        log.Fatalf("could not greet: %v", err)
    }
    log.Printf("Greeting: %s", r.Message)
}
```