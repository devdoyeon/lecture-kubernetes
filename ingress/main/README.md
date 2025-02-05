```
$ kubectl cp index.html ingress-nginx/pod-main:/usr/share/nginx/html/index.html
$ kubectl exec pod-main -n ingress-nginx -- cat /usr/share/nginx/html/index.html

<html>
        <head>
        <title>Main Page</title>
        </head>
<body>
        <p><h1> This page name is the main</h1><br>
        <a href="http://10.10.8.4:30080/order/">order</a><br>
        <a href="http://10.10.8.4:30080/shopping/">shopping</a><br>
        <a href="http://10.10.8.4:30080/customer/">customer</a><br>
        <a href="http://10.10.8.4:30080/">HOME</a>
</body>
</html>
```
