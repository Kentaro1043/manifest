# nextcloud

NFS対応のために回避策を適用している。

[回避策](https://github.com/nextcloud/helm/issues/10#issuecomment-1914066964)

```bash
sudo mkdir -p /srv/nfs/nextcloud
sudo chown -R nobody:nogroup /srv/nfs/nextcloud
```
