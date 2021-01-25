# tke-anp-tunnel

This repo aim to leverage [ANP](https://github.com/kubernetes-sigs/apiserver-network-proxy/) to enable communication between hub cluster and managed cluster by a tunnel for `Multi-Cloud` solution, currently, only support `push` mode.

## Build&Push image

```
export REGISTRY=huxl
export TAG=1.0
make docker-build/proxy-test-client
make docker-build/proxy-server
make docker-build/proxy-agent
make docker-build/http-test-server

make docker-push/proxy-test-client
make docker-push/proxy-server
make docker-push/proxy-agent
make docker-push/http-test-server

```

## Generate server/agent certificates

1.  Generate certificates
```
export REQ_CN=`<public-tunnel-server-ip>`
make certs
```
2.  Create `tke-tunnel-server` certificates on `hub-cluster` (server node)
```
kubectl create secret tls tke-anp-server-tls --key="certs/agent/private/proxy-master.key" --cert="certs/agent/issued/proxy-master.crt" -n tke
```
3.  Create `tke-tunnel-agent` certificates on `managed-cluster` (agent node)
```
kubectl create secret generic tke-anp-agent-tls --from-file=tls.crt=certs/agent/issued/proxy-agent.crt --from-file=tls.key=certs/agent/private/proxy-agent.key --from-file=ca.crt=certs/agent/issued/ca.crt -n tke
```

## Deploy server

```
kubectl  create -f tke-anp-server.yaml
```

## Deploy agent

```
kubectl  create -f tke-anp-agent.yaml
```
