```
$ kubectl cp index.html ingress-nginx/pod-customer:/usr/share/nginx/html/index.html
$ kubectl exec pod-customer -n ingress-nginx -- cat /usr/share/nginx/html/index.html

<html>
        <head>
        <title>Customer Page</title>
        </head>
<body>
        <p><h1> This page name is the main</h1><br>
        <a href="http://10.10.8.4:30080/order/">order</a><br>
        <a href="http://10.10.8.4:30080/shopping/">shopping</a><br>
        <a href="http://10.10.8.4:30080/">HOME</a>
</body>
</html>
```
