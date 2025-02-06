# Dashboard 설치
```bash
# Dashboard 설치 파일 적용
$ kubectl apply -f kube-dashboard.yaml
# https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# 리소스 확인
$ kubectl get pods -n kubernetes-dashboard

# 서비스 확인
$ kubectl get svc -n kubernetes-dashboard

# 서비스 타입 변경
$ kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
# service의 type을 NodePort로 변경

# 적용된 NodePort를 확인
$ kubectl get svc -n kubernetes-dashboard

# 관리자 ServiceAccount 생성
$ kubectl apply -f admin-user.yaml

# 생성된 admin-user의 Token을 조회
$ kubectl -n kubernetes-dashboard create token admin-user > ./admin-token

# kubectl proxy를 사용해 대시보드에 접속
$ kubectl proxy & https://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

# Service Type을 NodePort로 변경했다면, 해당 IP와 포트를 통해 접속한 후,
# admin-token의 내용으로 대시보드에 접속한다.
```

## Dashboard 로그인 만료 방지
```bash
$ kubectl edit deploy kubernetes-dashboard -n kubernetes-dashboard
# spec.template.spec.containers.args에
# - --token-ttl=0
# 플래그를 추가한다. ttl을 0으로 주면 만료되지 않는다.

# admin-user token 만료 시간 설정
$ kubectl -n kubernetes-dashboard create token admin-user --duration 720h > ./admin-token
# 720시간 후에 만료되는 토큰이 admin-token 파일에 담긴다.
```
