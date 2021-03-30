# Envoy proxy gRPC configuration

## Usage


Generate .pb file from .proto file and paste .pb to prepared directory
(hrservice.pb)

```
├── Dockerfile
├── README.md
├── envoy.yaml
└── proto
    └── hrservice.pb
```

Prepare Dockerfile

```
FROM envoyproxy/envoy-dev:bcc66c6b74c365d1d2834cfe15b847ae13be0eb6
COPY envoy.yaml /etc/envoy/envoy.yaml
COPY proto/hrservice.pb /tmp/envoy/proto.pb
```

On envoy.yaml, there are 3 configuration that we need to modify for supporting REST and point to your gRPC service.
(proto_descriptor path will be the same as what you configured on Dockerfile)

```
http_filters:                                                         
    - name: envoy.grpc_json_transcoder
    config:
        proto_descriptor: "/tmp/envoy/proto.pb"
        services: ["com.iphayao.grpc.service.HRService"]
```

On envoy.yaml, set gRPC service endpoint and port

```
  clusters:
  - name: grpcserver
    hosts:
    - socket_address:
        address: host.docker.internal
        port_value: 8080
```

Build docker
```
docker build -t envoy-v1 . 
```

Run docker
```
docker run -p 51051:51051 -d envoy:v1


```