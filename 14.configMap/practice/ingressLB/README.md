# 1. Ingress Controller 설치
```bash
$ wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.1/deploy/static/provider/baremetal/deploy.yaml 
$ mv deploy.yaml ingress-controller.yaml
```

# 2. Ingress Nginx를 구성할 때 LoadBalancer 추가하기
```bash
$ vi ingress-controller.yaml
# 365번 줄의 type을 LoadBalancer로 수정 
```

# 3. Ingress Controller 실행
```bash
$ kubectl apply -f ingress-controller.yaml
$ kubectl get svc -n ingress-nginx
# NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
# ingress-nginx-controller             LoadBalancer   10.104.140.31   10.10.8.100   80:31660/TCP,443:32402/TCP   33s
# ingress-nginx-controller-admission   ClusterIP      10.98.94.63     <none>        443/TCP                      33s
```

# 4. config.yaml
```bash
# config.yaml 파일 구성 후 적용
$ kubectl apply -f config.yaml
```

# 5. ingress-pod-service.yaml
```bash
# ingress-pod-service.yaml 파일 구성 후 적용
$ kubectl apply -f ingress-pod-service.yaml
```

# 6. ingress-path.yaml
```bash
# ingress-path.yaml 파일 구성 후 적용
$ kubectl apply -f ingress-path.yaml
```

# 7. Ingress Controller 서비스 port 확인
```bash
# NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
# ingress-nginx-controller   LoadBalancer   10.104.140.31   10.10.8.100   80:31660/TCP,443:32402/TCP   5m33s
```

# 8. Ingress 확인
```bash
$ kubectl get ingress
# NAME         CLASS   HOSTS   ADDRESS     PORTS   AGE
# service-lb   nginx   *       10.10.8.4   80      2m9s
```

# 9. service-lb Ingress 정보 확인
```bash
$ kubectl describe ingress service-lb
# Name:             service-lb
# Labels:           <none>
# Namespace:        default
# Address:          10.10.8.4
# Ingress Class:    nginx
# Default backend:  <default>
# Rules:
#   Host        Path  Backends
#   ----        ----  --------
#   *
#               /           svc-mainurl:80 (<error: endpoints "svc-mainurl" not found>)
#               /shopping   svc-shopping:80 (<error: endpoints "svc-shopping" not found>)
#               /customer   svc-customer:80 (<error: endpoints "svc-customer" not found>)
#               /order      svc-order:80 (<error: endpoints "svc-order" not found>)
# Annotations:  <none>
# Events:
#   Type    Reason  Age                   From                      Message
#   ----    ------  ----                  ----                      -------
#   Normal  Sync    104s (x2 over 2m28s)  nginx-ingress-controller  Scheduled for sync
```
