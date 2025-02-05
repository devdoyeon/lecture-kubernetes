```
$ kubectl cp index.html ingress-nginx/pod-order:/usr/share/nginx/html/index.html
$ kubectl exec pod-order -n ingress-nginx -- cat /usr/share/nginx/html/index.html

<html>
        <head>
        <title>Order Page</title>
        </head>
<body>
        <p><h1> This page name is the main</h1><br>
        <a href="http://192.168.108.21:30080/shopping/">shopping</a><br>
        <a href="http://192.168.108.21:30080/customer/">customer</a><br>
        <a href="http://192.168.108.21:30080/">HOME</a>
</body>
</html>
```
