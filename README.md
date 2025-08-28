# mainfest

## Tasks

### build

kustomizeプロジェクトをビルドする。

INPUTS: DIR

```bash
kustomize build $DIR --enable-alpha-plugins --enable-exec --enable-helm > built.yaml
```

### enc

sopsで暗号化する。

INPUTS: FILE

```bash
case "$FILE" in
  *.yaml) OUT="${FILE%.yaml}.enc.yaml" ;;
  *.yml)  OUT="${FILE%.yml}.enc.yml" ;;
  *)      OUT="${FILE}.enc" ;;
esac

sops -e "$FILE" > "$OUT"
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

## Bootstrap

### 1. sops用secret作成

```sh
kubectl create secret generic sops-age --namespace=argocd --from-file="$HOME/Library/Application Support/sops/age/keys.txt"
```

### 2. `argocd`をデプロイ

```sh
kubectl create ns argocd
kustomize build . --enable-alpha-plugins --enable-exec --enable-helm | kubectl apply -n argocd -f -
```

### 3. ArgoCDのUIからリポジトリなど設定

### 4. `argocd`、`traefik`を含む各アプリをSync
