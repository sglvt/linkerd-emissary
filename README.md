# linkerd-emissary

Note: when installing on a test environment with a single node, don't use `--ha` when installing Linkerd.

```
curl -sL https://run.linkerd.io/install | sh
export PATH=$PATH:$HOME/.linkerd2/bin
linkerd version
linkerd install --ha | kubectl apply -f -
linkerd check
```

## Reference
[Installing the Emissary ingress with the Linkerd service mesh](https://buoyant.io/2021/05/24/emissary-and-linkerd-the-best-of-both-worlds/)
[Emissary - Linkerd 2 integration](https://www.getambassador.io/docs/emissary/latest/howtos/linkerd2/)