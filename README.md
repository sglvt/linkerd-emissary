# linkerd-emissary

## Linkerd setup
Note: when installing on a test environment with a single node, don't use `--ha` when installing Linkerd.

```
curl -sL https://run.linkerd.io/install | sh
export PATH=$PATH:$HOME/.linkerd2/bin
linkerd version
linkerd install --ha | kubectl apply -f -
linkerd check
linkerd viz install | kubectl apply -f 
linkerd viz check
```


## Emissary setup
Deploy Emissary-ingress
```
kubectl apply -f https://www.getambassador.io/yaml/aes-crds.yaml && \
kubectl wait --for condition=established --timeout=90s crd -lproduct=aes && \
kubectl apply -f https://www.getambassador.io/yaml/aes.yaml && \
kubectl -n ambassador wait --for condition=available --timeout=90s deploy -lproduct=aes
kubectl -n ambassador get deploy ambassador -o yaml | \
  linkerd inject \
  --skip-inbound-ports 80,443 \
  --require-identity-on-inbound-ports 4183 - | \
  kubectl apply -f -
```

Configure Emissary-ingress to add Linkerd 2 Headers to requests
```
kubectl apply -f - <<EOF
apiVersion: getambassador.io/v2
kind: Module
metadata:
  name: ambassador
  namespace: ambassador
spec:
  config:
  add_linkerd_headers: true
EOF
```

## Sample app setup
```
kubectl create deployment sample-go-http-app \
  --image=serbangilvitu/sample-go-http-app:3 \
  --port=8081 \
  --dry-run=client -o yaml \
  | linkerd inject - | kubectl apply -f -
kubectl expose deployment sample-go-http-app
```

```
kubectl apply -f - <<EOF
apiVersion: getambassador.io/v2
kind: Mapping
metadata:
  name: sample-go-http-app
spec:
  hostname: "*"
  prefix: /sample/
  service: sample-go-http-app.default.svc.cluster.local:8081
EOF
```

## Reference
[Installing the Emissary ingress with the Linkerd service mesh](https://buoyant.io/2021/05/24/emissary-and-linkerd-the-best-of-both-worlds/)
[Emissary - Linkerd 2 integration](https://www.getambassador.io/docs/emissary/latest/howtos/linkerd2/)