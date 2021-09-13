# Helm

Source: https://helm.sh/docs/chart_template_guide/getting_started/

A Chart is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.

A Repository is the place where charts can be collected and shared. It's like Perl's CPAN archive or the Fedora Package Database, but for Kubernetes packages.

A Release is an instance of a chart running in a Kubernetes cluster.

## Helm create chart

- `helm create mychart`

- The above command will create below files in the ~/mychart directory. 

```
╰─ tree mychart
.
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```


## Templates

The most important component is the templates/ directory. This is where Helm finds the YAML definitions for your Services, Deployments and other Kubernetes objects.

You can put yam files from existing k8s project, but you can use template rendring engine to replace variables in a YAML file.
Helm uses go templates.(https://pkg.go.dev/text/template)

Template looks like this

```
apiVersion: v1
kind: Service
metadata:
name: {{ template "fullname" . }}
labels:
    chart: "{{ .Chart.Name }}-{{ .Chart.Version | replace "+" "_" }}"
spec:
type: {{ .Values.service.type }}
ports:
- port: {{ .Values.service.externalPort }}
    targetPort: {{ .Values.service.internalPort }}
    protocol: TCP
    name: {{ .Values.service.name }}
selector:
    app: {{ template "fullname" . }}
```

### Values

The template in service.yaml makes use of the Helm-specific objects .Chart and .Values.. The former provides metadata about the chart to your definitions such as the name, or version. The latter .Values object is a key element of Helm charts, used to expose configuration that can be set at the time of deployment.

The defaults for this object are defined in the `values.yaml`. Values file looks like this

```
image:
  repository: nginx
  pullPolicy: IfNotPresent

imagePullSecrets: []

serviceAccount:
  create: true
  name: "account1"

```

You can dry run your chart. 

`helm install --dry-run --debug ./mychart --set service.internalPort=8080`

you can also specify a YAML file containing overrides with the --values option.


To install,

`helm install example ./mychart`



# Anti Affinity


## Node affinity
The node affinity feature allows you to schedule workloads (pods) onto specific nodes, for example, to optimize for various resources, such as storage, CPU, and networking.


## Pod affinity and pod anti-affinity

Pod affinity allows you to specify that a pod should be scheduled on the same node as another pod. For example, you can use pod anti-affinity to ensure that the MySQL POD do not share nodes with other memory intensive workloads.


Very good doc for explanation of types of node affinity etc. - https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/


# Liveness and Readiness probes

Liveness probe checks the container health as we tell it do, and if for some reason the liveness probe fails, it restarts the container.

HTTP livenessProbe for an application.
```
livenessProbe:
  httpGet:
      scheme: HTTP
      path: /
      port: 8000
  initialDelaySeconds: 30
  timeoutSeconds: 30
```

We can also define a `Liveness command` to run to check on a service/pod or we can also `TCP Liveness` probe to check a TCP port.


Rediness probe, If the condition inside readiness probe passes, only then our application is ready to serve traffic.
We can specify a HTTP, TCP ans a command probes for it as well.

```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

> Startup probes are a newly developed feature. Liveness probes do not wait for readiness probes to succeed. If you want to wait before executing a liveness probe you should use initialDelaySeconds or a startupProbe. Startup probe allows our aplication to become ready.

```
startupProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 30
  periodSeconds: 10
```

