# Kubernetes 환경 구축
VMWare에서
<br/>
![image](https://github.com/user-attachments/assets/b58ff538-911c-47d2-a190-69f003033fb7)<br/>
해당 환경으로 master.labs.local을 만들고, Ubuntu live server를 설치한다.
<br/>
모든 호스트(master, node1, node2, node3)에서 설치해야 하지만 시간을 절약하기 위해 `master.labs.local`에 공통 요소를 설정한 후에 Clone을 만들 것이다.
<br/>
## master.labs.local에서 수행
### 기본 환경 구축
```bash
$ sudo -i
$ sed -i '/DNS/s/#DNS=/DNS=8.8.8.8 8.8.4.4/' /etc/systemd/resolved.conf # 네임 서버 변경
$ systemctl restart systemd-resolved.service # resolve 재시작

# IP, Gateway, NameServer 설정
$ vi /etc/netplan/50-cloud-init.yaml
# network:
#     ethernets:
#       enp2s1:
#         dhcp4: no
#         addresses:
#           - 10.10.8.3/24
#         routes:
#           - to: default
#             via: 10.10.8.2
#         nameservers:
#           addresses: [8.8.8.8]
#     version: 2

$ apt update
$ apt install chrony -y

$ vi /etc/chrony/chrony.conf
# :set nu
# :20,23s/^/#/
# server 203.248.240.140 iburst maxsources 2
$ systemctl restart chrony
$ timedatectl set-timezone Asia/Seoul # 타임 존 재설정

$ swapoff -a # 스왑 파일 비활성화
$ vi /etc/fstab # 파일을 열어서 /swap.img로 시작하는 라인 주석 처리 후 저장

$ echo -e 'root:dkagh1.\nroot:dkagh1.' | chpasswd # root 사용자 암호 dkagh1.으로 설정
$ echo "ubuntu  ALL=(ALL:ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ubuntu # sudo -i 시 암호 묻지 않게 하기

$ hostnamectl set-hostname master.labs.local # 호스트 네임 설정
$ vi /etc/hosts # 호스트 파일 등록
# 10.10.8.3       master  master.labs.local
# 10.10.8.4       node1   node1.labs.local
# 10.10.8.5       node2   node2.labs.local
# 10.10.8.6       node3   node3.labs.local

$ sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' \
  -e 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' \ 
  /etc/ssh/sshd_config # root 사용자 SSH 접근 허용

$ systemctl restart sshd # SSH 재시작
$ reboot
```
### Kubernetes 설정
```bash
# 명시적으로 로드, sudo modprobe br_netfilter를 실행한다.
$ cat << EOF | sudo tee /etc/modules-load.d/k8s.conf 
overlay 
br_netfilter 
EOF
$ modprobe overlay
$ modprobe br_netfilter

# 리눅스 노드의 ip tables가 브리지된 트래픽을 올바르게 보기 위한 요구 사항으로,
# sysctl 구성에서 net.bridge.bridge-nf-call-iptables를 1로 설정한다.
$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf 
net.bridge.bridge-nf-call-iptables  = 1 
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1 
EOF

# 재부팅 하지 않고 sysctl 파라미터 적용
$ sysctl --system

# containerd
$ apt install containerd -y
$ mkdir -p /etc/containerd
$ containerd config default | sudo tee /etc/containerd/config.toml

# SystemdCgroup 옵션이 true로 설정되면, containerd는 Cgroup 관리에 systemd를 사용하게 된다.
# 이는 Cgroup v2를 사용하는 시스템에서 특히 유용하다.
# Cgroup v2는 systemd와의 통합을 통해 더 효율적이고 일관된 리소스 관리를 제공한다.
$ sed -i '/SystemdCgroup/s/false/true/' /etc/containerd/config.toml

$ systemctl restart containerd.service # 재시작

# apt-transport-https, ca-certificates, curl, gpg 설치
$ apt-get install -y apt-transport-https ca-certificates curl gpg

# Kubernetes 패키지 저장소의 GPG 키를 다운로드하고, 다운로드한 키를 이진 형식으로 변환 후
# 변환된 키를 APT가 사용할 수 있는 위치에 저장한다.
$ curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg \ 
  --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Kubernetes 패키지 저장소를 APT 소스 목록에 추가
$ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

$ apt update
$ apt-cache madison kubeadm

# 위 과정에서 찾은 버전을 아래에 적용
$ apt install -y kubelet=1.32.1-1.1 kubeadm=1.32.1-1.1 kubectl=1.32.1-1.1

# 패키지의 버전이 자동으로 업데이트 되지 않도록 고정
$ apt-mark hold kubelet kubeadm kubectl

# 서비스 시작 및 활성
$ systemctl enable --now kubelet
```
---
>[!Note]
>1. master.labs.local 시스템을 끈다.
>2. VMWare에서 Full Clone으로 `node1.labs.local`, `node2.labs.local`, `node3.labs.local`을 생성한다.
>3. node1, node2, node3의 `/etc/netplan/50-cloud-init.yaml` 파일의 IP를 각각 4, 5, 6으로 변경한다.
>4. node1, node2, node3에서 `sudo hostnamectl set-hostname node{n}.labs.local`로 호스트 네임을 변경한다.
>5. node1, node2, node3를 reboot한다.
---
## master.labs.local에서 수행
```bash
$ sudo -i

# Kubernetes 클러스터를 초기화
# Pod들이 사용할 네트워크 범위를 명시적으로 정의하고 클러스터의 API 서버가 다른 노드들과
# 통신할 수 있는 주소를 지정한다.
$ kubeadm init --pod-network-cidr=10.11.0.0/16 \
  --apiserver-advertise-address=10.10.8.3

# kube 환경 설정
$ mkdir -p $HOME/.kube
$ cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ chown $(id -u):$(id -g) $HOME/.kube/config

# Kubectl 자동 완성 기능 설치
$ apt install bash-completion -y
$ source <(kubectl completion bash)
$ echo "source <(kubectl completion bash)" >> ~/.bashrc
```

## node1, node2, node3에서 각각 수행
```bash
$ sudo -i

# master.labs.local에서 kubeadm init의 작업 결과로 화면에 출력된 값을 토큰에 적용
$ kubeadm join 10.10.8.3:6443 --token {} --discovery-token-ca-cert-hash {}
```

## master.labs.local에서 수행
```bash
$ sudo -i
$ mkdir ~/cni; cd cni

# Calico는 Kubernetes 클러스터의 네트워킹과 보안을 관리하기 위한 강력한 도구이다.
# Pod 네트워킹, 네트워크 정책, 고성능 네트워크, BGP 지원 등의 기능을 통해
# 클러스터의 네트워크를 효율적으로 관리하고 보안을 강화한다.
$ kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml

$ wget https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml
$ kubectl create -f custom-resources.yaml

# Pod의 cidr 값으로 변경
$ sed -i 's/192.168.0.0\/16/10.11.0.0\/16/' custom-resources.yaml

# Calico 적용
$ kubectl create -f custom-resources.yaml

# Calico Pod 확인
$ kubectl get pods -A | grep tigera
$ kubectl get pods -A | grep calico

# 설치 후 NameSpace 보기
$ kubectl get all -n kube-system

# Worker Node의 등록 상태 확인
$ kubectl get nodes
# NAME                STATUS   ROLES           AGE   VERSION
# master.labs.local   Ready    control-plane   16d   v1.32.1
# node1.labs.local    Ready    <none>          16d   v1.32.1
# node2.labs.local    Ready    <none>          16d   v1.32.1
# node3.labs.local    Ready    <none>          16d   v1.32.1
```

## kubectl을 k로 실행하기
alias를 추가하면 된다.
```bash
# .bashrc 파일 오픈
$ vi ~/.bashrc

# .bashrc 파일 내에 alias 명령어 추가하고 :wq 해서 빠져나오기
# alias k='kubectl'

# 변경 사항 적용
$ source ~/.bashrc

# k로 kubectl 작동되는지 확인해 보기
$ k get nodes
# NAME                STATUS   ROLES           AGE     VERSION
# master.labs.local   Ready    control-plane   9m28s   v1.32.1
# node1.labs.local    Ready    <none>          4m10s   v1.32.1
# node2.labs.local    Ready    <none>          4m11s   v1.32.1
# node3.labs.local    Ready    <none>          4m14s   v1.32.1
```
