# 1. 파일 생성
```bash
$ echo "Hello, World" >> index.html
```

# 2. ConfigMap 생성
```bash
$ kubectl create configmap index-file --from-file index.html
# --from-file에 키를 지정하지 않으면 파일 이름이 키로,
# 파일 내용이 값으로 저장된다.
```

# 3. ConfigMap 확인
```bash
$ kubectl describe cm index-file
# Name:         index-file
# Namespace:    default
# Labels:       <none>
# Annotations:  <none>
#
# Data
# ====
# index.html:
# ----
# Hello, World
#
#
# BinaryData
# ====
#
# Events:  <none>
```

# 4. 키를 지정하여 생성
```bash
$ kubectl create configmap index-file-key --from-file mykey=index.html
$ kubectl describe cm index-file-key
# Name:         index-file-key
# Namespace:    default
# Labels:       <none>
# Annotations:  <none>
#
# Data
# ====
# mykey:
# ----
# Hello, World
#
#
# BinaryData
# ====
#
# Events:  <none>
```
