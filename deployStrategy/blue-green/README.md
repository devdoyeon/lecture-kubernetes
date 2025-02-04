<code>
$ kubectl apply -f blue.yaml
$ kubectl describe services test-service

 Name:              test-service 
 Namespace:         default 
 Labels:            <none> 
 Annotations:       <none> 
 Selector:          app=test-pod,  color=blue 
 Type:              ClusterIP 
 IP Family Policy:  SingleStack 
 IP Families:       IPv4 
 IP:                10.97.88.35 
 IPs:               10.97.88.35 
 Port:              <unset>  80/TCP 
 TargetPort:        8080/TCP 
 Endpoints:         10.36.0.1:8080,10.42.0.1:8080,10.44.0.1:8080 + 1 more... 
 Session Affinity:  None 
 Events:            <none>

$ kubectl apply -f green.yaml
$ kubectl patch service test-service --patch-file blue-green.yaml

$ kubectl descirbe services test-service

 Name:              test-service 
 Namespace:         default 
 Labels:            <none> 
 Annotations:       <none> 
 Selector:          app=test-pod,  color=green 
 Type:              ClusterIP 
 IP Family Policy:  SingleStack 
 IP Families:       IPv4 
 IP:                10.97.88.35 
 IPs:               10.97.88.35 
 Port:              <unset>  80/TCP 
 TargetPort:        8080/TCP 
 Endpoints:         10.36.0.2:8080,10.36.0.3:8080,10.42.0.2:8080 + 1 more... 
 Session Affinity:  None 
 Events:            <none> 
</code>
