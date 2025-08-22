# jellyfin

Prerequired

```shell
kubectl create secret generic cookiefile \
  --from-file=cookies.txt=./cookies.txt \
  --namespace=jellyfin
```
