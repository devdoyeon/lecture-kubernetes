# 1. ConfigMap 생성
```bash
$ kubectl create configmap log-level-configmap \
  --from-literal LOG_LEVEL=DEBUG
$ kubectl create configmap start-k8s \                                        --from-literal k8s=kubernetes \
  --from-literal container=docker
```
# 2. deployment 생성
```bash
$ kubectl apply -f web-apaches.yaml
```
# 3. Pod 확인
```bash
$ kubectl get pods
# NAME                             READY   STATUS    RESTARTS   AGE
# dp-web-apaches-599755789b-fnq7q   1/1     Running   0          5s
# dp-web-apaches-599755789b-gfdt4   1/1     Running   0          5s
# dp-web-apaches-599755789b-htxb6   1/1     Running   0          5s
```
# 4. Pod의 env 확인
키 이름으로 파일이 생성됨.
```bash
$ kubectl exec dp-web-apaches-599755789b-fnq7q -- ls /etc/config
# container
# k8s
```
# 5. 
