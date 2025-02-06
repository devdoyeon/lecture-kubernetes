# kubectl top
kubectl top을 사용하기 위해 metrics-server를 설치한다.<br>
top은 Pod나 Deployment와 같은 객체의 CPU, Memory 등 리소스 현황을 볼 수 있는 유용한 도구이다.<br>
```bash
$ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
$ kubectl get pods -n kube-system | grep metrics-server

# metric server 설정 변경

$ kubectl edit deploy metrics-server -n kube-system
# spec.template.spec.containers.args에
# - --kubelet-insecure-tls 플래그 추가

# 변경 사항 적용
$ kubectl rollout restart deploy metrics-server -n kube-system

# top 사용해 보기
$ kubectl top pod
```
