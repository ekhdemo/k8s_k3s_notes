## Create My First Helm Chart

For a typical cloud-native application with a 3-tier architecture, the diagram below illustrates how it might be described in terms of Kubernetes objects. In this example, each tier consists of a Deployment and Service object, and may additionally define ConfigMap or Secret objects. Each of these objects are typically defined in separate YAML files, and are fed into the kubectl command line tool.

```
![three-tier-kubernetes-architecture](https://github.com/ekhdemo/k8s_k3s_notes/blob/main/helm-notes/three-tier-kubernetes-architecture.png?raw=true "Three Tier Kubernetes Architecture")
```

### Step 1: Generate your first chart

Use this command to create a new chart named mychart in a new directory:

```
helm create mychart
Creating mychart
```

```
tree

mychart
   ├── Chart.yaml
   ├── charts
   ├── templates
   │   ├── NOTES.txt
   │   ├── _helpers.tpl
   │   ├── deployment.yaml
   │   ├── hpa.yaml
   │   ├── ingress.yaml
   │   ├── service.yaml
   │   ├── serviceaccount.yaml
   │   └── tests
   │       └── test-connection.yaml
   └── values.yaml
```
**Templates**

This is where Helm finds the YAML definitions for your Services, Deployments and other Kubernetes objects. 

Open the service.yaml file to see what this looks like:

```
cat mychart/templates/service.yaml
```
```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    {{- include "mychart.selectorLabels" . | nindent 4 }}
```

We can do a dry-run of a helm install and enable debug to inspect the generated definitions:

```
helm install --dry-run --generate-name --debug ./mychart
```

```
install.go:178: [debug] Original chart version: ""
install.go:199: [debug] CHART PATH: /Users/fatih/Desktop/k8s_k3s_notes/helm-notes/mychart

NAME: mychart-1641021661
LAST DEPLOYED: Sat Jan  1 11:21:03 2022
NAMESPACE: default
STATUS: pending-install
REVISION: 1
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
affinity: {}
autoscaling:
  enabled: false
  maxReplicas: 100
  minReplicas: 1
  targetCPUUtilizationPercentage: 80
fullnameOverride: ""
image:
  pullPolicy: IfNotPresent
  repository: nginx
  tag: ""
imagePullSecrets: []
ingress:
  annotations: {}
  className: ""
  enabled: false
  hosts:
  - host: chart-example.local
    paths:
    - path: /
      pathType: ImplementationSpecific
  tls: []
nameOverride: ""
nodeSelector: {}
podAnnotations: {}
podSecurityContext: {}
replicaCount: 1
resources: {}
securityContext: {}
service:
  port: 80
  type: ClusterIP
serviceAccount:
  annotations: {}
  create: true
  name: ""
tolerations: []

HOOKS:
---
# Source: mychart/templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "mychart-1641021661-test-connection"
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: mychart-1641021661
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['mychart-1641021661:80']
  restartPolicy: Never
MANIFEST:
---
# Source: mychart/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mychart-1641021661
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: mychart-1641021661
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
---
# Source: mychart/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mychart-1641021661
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: mychart-1641021661
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: mychart-1641021661
---
# Source: mychart/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mychart-1641021661
  labels:
    helm.sh/chart: mychart-0.1.0
    app.kubernetes.io/name: mychart
    app.kubernetes.io/instance: mychart-1641021661
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: mychart
      app.kubernetes.io/instance: mychart-1641021661
  template:
    metadata:
      labels:
        app.kubernetes.io/name: mychart
        app.kubernetes.io/instance: mychart-1641021661
    spec:
      serviceAccountName: mychart-1641021661
      securityContext:
        {}
      containers:
        - name: mychart
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}

NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart-1641021661" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

**Values**

The template in service.yaml makes use of the Helm-specific objects .Chart and .Values.. The former provides metadata about the chart to your definitions such as the name, or version. The latter .Values object is a key element of Helm charts, used to expose configuration that can be set at the time of deployment. The defaults for this object are defined in the values.yaml file. Try changing the default value for service.internalPort and execute another dry-run, you should find that the targetPort in the Service and the containerPort in the Deployment changes. The service.internalPort value is used here to ensure that the Service and Deployment objects work together correctly. The use of templating can greatly reduce boilerplate and simplify your definitions.

If a user of your chart wanted to change the default configuration, they could provide overrides directly on the command-line:

```
helm install --dry-run --generate-name --debug ./mychart --set service.internalPort=8080
```

```
install.go:178: [debug] Original chart version: ""
install.go:199: [debug] CHART PATH: /Users/fatih/Desktop/k8s_k3s_notes/helm-notes/mychart

NAME: mychart-1641022218
LAST DEPLOYED: Sat Jan  1 11:30:19 2022
NAMESPACE: default
STATUS: pending-install
REVISION: 1
USER-SUPPLIED VALUES:
service:
  internalPort: 8080

COMPUTED VALUES:
affinity: {}
autoscaling:
  enabled: false
  maxReplicas: 100
  minReplicas: 1
  targetCPUUtilizationPercentage: 80
fullnameOverride: ""
image:

.
.
.

      containers:
        - name: mychart
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}

NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart-1641022218" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```


### Step 2: Deploy your first chart

The chart you generated in the previous step is set up to run an NGINX server exposed via a Kubernetes Service. By default, the chart will create a ClusterIP type Service, so NGINX will only be exposed internally in the cluster. To access it externally, we'll use the NodePort type instead. We can also set the name of the Helm release so we can easily refer back to it. Let's go ahead and deploy our NGINX chart using the helm install command:

```
helm install example ./mychart --set service.type=NodePort
```
```
NAME: example
LAST DEPLOYED: Sat Jan  1 11:34:59 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services example-mychart)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
The output of helm install displays a handy summary of the state of the release, what objects were created, and the rendered NOTES.txt file to explain what to do next. Run the commands in the output to get a URL to access the NGINX service and pull it up in your browser.

```
![Nginx Welcome Page](https://raw.githubusercontent.com/ekhdemo/k8s_k3s_notes/main/helm-notes/nginx-server.png "Nginx Welcome Page")
```

If all went well, you should see the NGINX welcome page as shown above. Congratulations! You've just deployed your very first service packaged as a Helm chart!


### Step 3: Modify chart to deploy a custom service

To do ...


### Step 4: Package it all up to share

To do ...




```
```


**Credit:**

1- [Bitnami.com](https://docs.bitnami.com/tutorials/create-your-first-helm-chart/)
