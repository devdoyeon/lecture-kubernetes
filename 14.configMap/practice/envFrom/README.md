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
# dp-web-apaches-57cdffc8ff-7mfph   1/1     Running   0          19s
# dp-web-apaches-57cdffc8ff-cdll5   1/1     Running   0          19s
# dp-web-apaches-57cdffc8ff-cwrjn   1/1     Running   0          19s
```
# 4. Pod의 env 확인
```bash
$ kubectl exec dp-web-apaches-57cdffc8ff-7mfph -- env
# PATH=/usr/local/apache2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# HOSTNAME=dp-web-apaches-57cdffc8ff-7mfph
# HTTPD_PREFIX=/usr/local/apache2
# HTTPD_VERSION=2.4.63
# HTTPD_SHA256=88fc236ab99b2864b248de7d49a008ec2afd7551e64dce8b95f58f32f94c46ab
# HTTPD_PATCHES=
# LOG_LEVEL=DEBUG
# container=docker # 이 부분을 확인하면
# k8s=kubernetes   # env가 제대로 작성된 것을 확인할 수 있다.
# KUBERNETES_PORT_443_TCP_PROTO=tcp
# KUBERNETES_PORT_443_TCP_PORT=443
# KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
# KUBERNETES_SERVICE_HOST=10.96.0.1
# KUBERNETES_SERVICE_PORT=443
# KUBERNETES_SERVICE_PORT_HTTPS=443
# KUBERNETES_PORT=tcp://10.96.0.1:443
# KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
# HOME=/root
```
