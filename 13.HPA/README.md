# HPA
HPA를 사용하기 위해서는 metrics-server를 설치해야 한다.
```bash
$ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
$ kubectl get pods -n kube-system | grep metrics-server

# metric server 설정 변경

$ kubectl edit deploy metrics-server -n kube-system
# spec.template.spec.containers.args에
# - --kubelet-insecure-tls 플래그 추가

# 변경 사항 적용
$ kubectl rollout restart deploy metrics-server -n kube-system
```
