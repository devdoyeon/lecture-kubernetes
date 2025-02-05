# NFS 설치하기
## master.labs.local에서
```bash
$ systemctl disable --now ufw # 방화벽 해제

$ sudo apt update 
$ sudo apt install nfs-kernel-server # nfs-kernel-server 설치

$ mkdir -m 777 /{data,html} 

$ cat << EOF >  /etc/exports 
/data 192.168.108.0/24(rw,sync,no_subtree_check,no_root_squash) 
/html 192.168.108.0/24(rw,sync,no_subtree_check,no_root_squash) 
EOF

$ exportfs -a 
$ systemctl restart nfs-kernel-server 
```

## node1, node2, node3에서
```bash
$ sudo -i 
$ apt install nfs-common 
$ apt update -y 
```
---
<br>

# kustomize
kustomize 활용해서 build.yaml 파일 만들기기

```
kubectl kustomize . -o build.yaml
```
