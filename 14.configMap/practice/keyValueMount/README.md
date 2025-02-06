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
NAME                             READY   STATUS    RESTARTS   AGE
dp-web-apaches-f857f965f-hndzw   1/1     Running   0          8s
dp-web-apaches-f857f965f-pfd9b   1/1     Running   0          8s
dp-web-apaches-f857f965f-pq8qp   1/1     Running   0          8s
```
# 4. Pod의 env 확인
키 이름으로 파일이 생성된다.
```bash
$ kubectl exec dp-web-apaches-f857f965f-hndzw -- ls /etc/config
# k8s_fullname
```
