## cmd로 ConfigMap 생성
```bash
$ kubectl create configMap mysql-config \
  --from-literal MYSQL_ROOT_PASSWORD=dkagh1. \
  --from-literal MYSQL_DATABASE=kubernetes
```
