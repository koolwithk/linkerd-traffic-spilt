# linkerd-traffic-spilt
linkerd-traffic-spilt example demo

![alt text](https://github.com/koolwithk/linkerd-traffic-spilt/blob/main/linkerd-trafficsplit.png?raw=true)

### Deploy apps and trafficsplit

```
kubectl create ns linkerd-test
kubectl annotate ns linkerd-test linkerd.io/inject=enabled

kubectl apply -f appv1.yml -n linkerd-test
kubectl apply -f appv2.yml -n linkerd-test
kubectl apply -f trafficsplit.yml -n linkerd-test
kubectl apply -f app-service.yml -n linkerd-test
```

## How to see the traffic split status?
```
#send traffic to app-service service from test pod
kubect -n linkerd-test exec -it <test_pod_name> -- bash

#inside test pod
apt install update -y && apt install curl -y
while(true) ; do echo test; curl -s  app-service | grep works ; done

#Will show around 90:10% traffic split between appv1 and appv2

[root@lp-kmaster-1 ~]# linkerd -n linkerd-test viz stat deploy
NAME    MESHED   SUCCESS      RPS   LATENCY_P50   LATENCY_P95   LATENCY_P99   TCP_CONN
appv1      1/1   100.00%   4.0rps           1ms           1ms           2ms          4
appv2      1/1   100.00%   0.8rps           1ms           2ms           2ms          4

```

## Note:

1. Test pod should be injected with linkerd-proxy sidecar
2. Linkerd viz should be installed
3. To expose this service outside of cluster we need to inject the linkerd-proxy sidecar into ingress controller

## Reference :

1. https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-split/v1alpha2/traffic-split.md
2. https://linkerd.io/2.10/features/traffic-split/
3. https://linkerd.io/2019/07/11/announcing-linkerd-2.4/
