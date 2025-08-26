# mainfest

## Tasks

### build

kustomizeプロジェクトをビルドする。

INPUTS: DIR

```bash
kustomize build $DIR --enable-alpha-plugins --enable-exec --enable-helm > built.yaml
```

## 事前準備

### 1. ageで鍵生成

```sh
age-keygen -o keys.txt 
```

### 2. 公開鍵を`.sops.yaml`に突っ込む

### 3. 鍵を移動

macOS

```sh
mkdir -p "$HOME/Library/Application Support/sops/age"
mv keys.txt "$HOME/Library/Application Support/sops/age"
```

Linux

```sh
mkdir -p "$XDG_CONFIG_HOME/sops/age"
mv keys.txt"$XDG_CONFIG_HOME/sops/age"
mv keys.txt 
```

## secretの入れ方

### 1. 暗号化

```sh
sops -e some-secret.yaml > some-secret.enc.yaml
```

### 2. 設定

`ksops.yaml`

```yaml
apiVersion: viaduct.ai/v1
kind: ksops
metadata:
  name: ksops
  annotations:
    config.kubernetes.io/function: |
      exec:
        path: ksops
files:
  - ./secrets/some-secret.enc.yaml
```

## Bootstrap

### 1. sops用secret作成

```sh
kubectl create secret generic sops-age --namespace=argocd --from-file="$HOME/Library/Application Support/sops/age/keys.txt"
```

### 2. `argocd`、`nginx-ingress`をデプロイ

```sh
kubectl create ns argocd
kustomize build . --enable-alpha-plugins --enable-exec --enable-helm | kubectl apply -n argocd -f -
```

### 3. ArgoCDのUIからリポジトリなど設定

### 4. `argocd`、`nginx-ingress`を含む各アプリをSync
