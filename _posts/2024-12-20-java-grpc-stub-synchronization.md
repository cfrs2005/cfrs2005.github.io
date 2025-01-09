---
title: Java gRPC 多种同步方式存根详解
date: 2024-12-20 08:00:00 +0800
categories: [Technology, Java, gRPC]
tags: [Java, gRPC, Stub, Synchronization]
img_path: '/assets/img/202412/'
image:
 path: java-grpc-stub.webp
 alt: 探索 Java gRPC 中多种同步方式的存根实现。
---

gRPC 是一种现代的远程过程调用（RPC）框架，支持多种语言和平台。Java gRPC 提供了多种同步方式的存根（Stub）实现，帮助开发者在不同的应用场景中灵活使用 gRPC。本文将详细介绍 Java gRPC 的同步、异步和半同步存根的实现及其适用场景。

### 1. 阻塞调用示例

在阻塞调用中，客户端会等待服务器响应。

#### Proto 文件示例
```proto
syntax = "proto3";

service MyService {
    rpc GetData (Request) returns (Response);
}

message Request {
    string query = 1;
}

message Response {
    string result = 1;
}
```

#### Java 阻塞调用示例
```java
// 创建阻塞存根
MyServiceGrpc.MyServiceBlockingStub blockingStub = MyServiceGrpc.newBlockingStub(channel);

// 发起请求并等待响应
Request request = Request.newBuilder().setQuery("example").build();
Response response = blockingStub.getData(request);
System.out.println("Response: " + response.getResult());
```

### 2. 异步调用示例

在异步调用中，客户端不会阻塞，可以在等待响应的同时执行其他操作。

#### Java 异步调用示例
```java
// 创建异步存根
MyServiceGrpc.MyServiceStub asyncStub = MyServiceGrpc.newStub(channel);

// 发起请求，使用回调处理响应
Request request = Request.newBuilder().setQuery("example").build();
asyncStub.getData(request, new StreamObserver<Response>() {
    @Override
    public void onNext(Response response) {
        System.out.println("Response: " + response.getResult());
    }

    @Override
    public void onError(Throwable t) {
        System.err.println("Error: " + t.getMessage());
    }

    @Override
    public void onCompleted() {
        System.out.println("Request completed.");
    }
});
```

### 3. 流式调用示例

流式调用允许客户端或服务器发送多个消息。

#### Proto 文件示例（流式调用）
```proto
service MyService {
    rpc StreamData (stream Request) returns (stream Response);
}
```

#### Java 流式调用示例
```java
// 创建流式存根
MyServiceGrpc.MyServiceStub asyncStub = MyServiceGrpc.newStub(channel);

// 创建一个流式请求观察者
StreamObserver<Request> requestObserver = asyncStub.streamData(new StreamObserver<Response>() {
    @Override
    public void onNext(Response response) {
        System.out.println("Response: " + response.getResult());
    }

    @Override
    public void onError(Throwable t) {
        System.err.println("Error: " + t.getMessage());
    }

    @Override
    public void onCompleted() {
        System.out.println("Streaming completed.");
    }
});

// 发送多个请求
requestObserver.onNext(Request.newBuilder().setQuery("data1").build());
requestObserver.onNext(Request.newBuilder().setQuery("data2").build());
requestObserver.onCompleted(); // 完成请求
```

### 存根生成

在 gRPC 中，当你根据 `.proto` 文件生成代码时，会生成多种类型的存根：

- **阻塞存根**（例如 `MyServiceBlockingStub`）：用于阻塞调用。
- **异步存根**（例如 `MyServiceStub`）：用于异步调用。
- **流式存根**：流式调用的方法会根据需求生成相应的流式存根。

因此，生成的代码会包含所有这三种类型的存根，供开发者选择适合的调用方式。你可以根据需求使用不同的存根来满足应用场景。