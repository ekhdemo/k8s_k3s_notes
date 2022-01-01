### Kubectl config get-contexts / use-context command

```
kubectl config get-contexts
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         default    default    default    
          minikube   minikube   minikube   default
```

```
kubectl config use-context minikube
Switched to context "minikube".
```


```
kubectl get pod -A                 
NAMESPACE     NAME                               READY   STATUS    RESTARTS        AGE
kube-system   coredns-78fcd69978-mxkj9           1/1     Running   0               4h31m
kube-system   etcd-minikube                      1/1     Running   0               4h31m
kube-system   kube-apiserver-minikube            1/1     Running   0               4h31m
kube-system   kube-controller-manager-minikube   1/1     Running   0               4h31m
kube-system   kube-proxy-xj5wp                   1/1     Running   0               4h31m
kube-system   kube-scheduler-minikube            1/1     Running   0               4h31m
kube-system   storage-provisioner                1/1     Running   2 (3h35m ago)   4h31m
```

```
kubectl config use-context default 
Switched to context "default".
```

```
kubectl get pod -A                
NAMESPACE       NAME                                          READY   STATUS    RESTARTS        AGE
kube-system     metrics-server-9cf544f65-qkkn2                1/1     Running   2 (6d10h ago)   8d
kube-system     local-path-provisioner-64ffb68fd-885js        1/1     Running   2 (6d10h ago)   8d
kube-system     coredns-85cb69466-qhnxl                       1/1     Running   2 (6d10h ago)   8d
ingress-nginx   svclb-my-ingress-nginx-controller-mjhqh       2/2     Running   0               6d2h
ingress-nginx   svclb-my-ingress-nginx-controller-xvljh       2/2     Running   0               6d2h
ingress-nginx   svclb-my-ingress-nginx-controller-d6rm4       2/2     Running   0               6d2h
default         web2-5d47994f45-6qfv9                         1/1     Running   0               6d
wordpress       my-release-mariadb-0                          1/1     Running   0               5d20h
default         demo-dep1-9dbbc6f56-zkgkn                     1/1     Running   0               5d
default         demo-dep1-9dbbc6f56-r9246                     1/1     Running   0               5d
default         web-79d88c97d6-p2sc6                          1/1     Running   0               3d19h
ingress-nginx   svclb-my-ingress-nginx-controller-vgm52       2/2     Running   2 (3d18h ago)   6d2h
wordpress       my-release-wordpress-847db46578-chk2f         1/1     Running   0               3d19h
wordpress       my-release-wordpress-847db46578-c7s57         1/1     Running   0               3d19h
default         demo-dep1-9dbbc6f56-xvs27                     1/1     Running   0               3d18h
ingress-nginx   my-ingress-nginx-controller-b9d8cddf4-t4872   1/1     Running   0               3d19h 
```


```
kubectl get ns    
NAME              STATUS   AGE
default           Active   8d
kube-system       Active   8d
kube-public       Active   8d
kube-node-lease   Active   8d
ingress-nginx     Active   6d3h
wordpress         Active   5d20h
demo              Active   36m
```
