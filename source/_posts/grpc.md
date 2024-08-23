---
title: grpc point
date: 2024-08-15 10:59:00
tags: point
categories: grpc
---

# proto file

> .proto file that defines the gRPC service and messages



# compile

> Use protoc to generate client and server code for both languages

When you compile `.proto` files using `protoc`, it generates two Go files: `xxx.pb.go` and `xxx_grpc.pb.go`. These files are essential for both client and server implementations in a gRPC service. Here's how they fit into the architecture:

### `xxx.pb.go`
- **Purpose**: Contains the Go representations of the protocol buffer messages defined in the `.proto` file.
- **Usage**: Used by both client and server to serialize and deserialize the messages.
- **Contents**: 
  - Go structs for each message type.
  - Methods for marshaling and unmarshaling these structs to and from binary format.

### `xxx_grpc.pb.go`
- **Purpose**: Contains the gRPC service definitions and client/server interfaces.
- **Usage**: 
  - **Server**: Implements the server-side logic by embedding the generated server interface.
  - **Client**: Uses the generated client interface to make RPC calls.
- **Contents**:
  - Server interface with methods corresponding to the RPCs defined in the `.proto` file.
  - Client interface with methods to call the RPCs.
  - Registration functions to register the server with a gRPC server.
  - Handler functions to handle incoming RPC calls.

# client and server

### Client Code

- **References**: Both `xxx.pb.go` and `xxx_grpc.pb.go`.
- **Functionality**: 
  - Uses the client interface from `xxx_grpc.pb.go` to make RPC calls.
  - Constructs request messages using the structs from `xxx.pb.go`.

### Server Code
- **References**: Both `xxx.pb.go` and `xxx_grpc.pb.go`.
- **Functionality**: 
  - Implements the server interface from `xxx_grpc.pb.go`.
  - Handles incoming requests and constructs response messages using the structs from `xxx.pb.go`.

### Example
In the provided example:

- **Client Code** (`examples/helloworld/greeter_client/main.go`):
  - Uses `pb.NewGreeterClient` from `helloworld_grpc.pb.go` to create a client.
  - Constructs `HelloRequest` messages using the struct from `helloworld.pb.go`.

- **Server Code** (`examples/helloworld/greeter_server/main.go`):
  - Implements `GreeterServer` interface from `helloworld_grpc.pb.go`.
  - Constructs `HelloReply` messages using the struct from `helloworld.pb.go`.

**This architecture ensures that both client and server have a consistent and type-safe way to communicate using the protocol buffer messages and gRPC service definitions.**