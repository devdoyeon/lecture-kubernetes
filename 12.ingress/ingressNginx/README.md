```
$ kubectl apply -f ingress-path.yaml
$ kubectl apply -f ingress-controller.yaml
$ kubectl apply -f ingress-pod-service.yaml

$ kubectl cp index.html ingress-nginx/pod-main:/usr/share/nginx/html/index.html
$ kubectl cp order/ ingress-nginx/pod-order:/usr/share/nginx/html/
$ kubectl cp shopping/ ingress-nginx/pod-shopping:/usr/share/nginx/html/
$ kubectl cp customer/ ingress-nginx/pod-customer:/usr/share/nginx/html/
```
