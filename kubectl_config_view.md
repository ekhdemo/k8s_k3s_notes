
### kubectl config view command

```
kubectl config view 
```
```                
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.168.1.30:6443
  name: default
- cluster:
    certificate-authority: /Users/fatih/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Fri, 31 Dec 2021 13:19:03 +04
        provider: minikube.sigs.k8s.io
        version: v1.24.0
      name: cluster_info
    server: https://192.168.64.3:8443
  name: minikube
contexts:
- context:
    cluster: default
    user: default
  name: default
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Fri, 31 Dec 2021 13:19:03 +04
        provider: minikube.sigs.k8s.io
        version: v1.24.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
- name: minikube
  user:
    client-certificate: /Users/fatih/.minikube/profiles/minikube/client.crt
    client-key: /Users/fatih/.minikube/profiles/minikube/client.key
```

-----
```
kubectl config view --raw
```
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0 ...

    server: https://192.168.1.30:6443
  name: default
- cluster:
    certificate-authority: /Users/fatih/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Fri, 31 Dec 2021 13:19:03 +04
        provider: minikube.sigs.k8s.io
        version: v1.24.0
      name: cluster_info
    server: https://192.168.64.3:8443
  name: minikube
contexts:
- context:
    cluster: default
    user: default
  name: default
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Fri, 31 Dec 2021 13:19:03 +04
        provider: minikube.sigs.k8s.io
        version: v1.24.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0t ...
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS ...
- name: minikube
  user:
    client-certificate: /Users/fatih/.minikube/profiles/minikube/client.crt
    client-key: /Users/fatih/.minikube/profiles/minikube/client.key
```
