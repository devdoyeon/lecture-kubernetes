```
$ kubectl cp index.html ingress-nginx/pod-shopping:/usr/share/nginx/html/index.html
$ kubectl exec pod-shopping -n ingress-nginx -- cat /usr/share/nginx/html/index.html

<html>
        <head>
        <title>Shopping Page</title>
        </head>
<body>
        <p><h1> This page name is the main</h1><br>
        <a href="http://192.168.108.21:30080/order/">order</a><br>
        <a href="http://192.168.108.21:30080/customer/">customer</a><br>
        <a href="http://192.168.108.21:30080/">HOME</a>
</body>
</html>
```
