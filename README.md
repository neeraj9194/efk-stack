# EFK Stack

Elasticsearch, Fluentd, and Kibana (EFK) stack is a centralized logging solution.

![img1](https://platform9.com/wp-content/uploads/2020/07/elastic-kibana.png)

## Setup

- Install elastic search using helm `helm repo add elastic https://helm.elastic.co`, `helm repo update`

- Install elasticsearch `helm install elasticsearch elastic/elasticsearch -n efk-monitoring --set persistence.enabled=false,replicas=1`

- Install Kibana `helm install kibana elastic/kibana --set env.ELASTICSEARCH_URL=http://elasticsearch-master:9200`

- Portforward kibana to localhost `kubectl port-forward deployment/kibana-kibana 5601`

- Deploy django app with a fluentd sidecar `kubectl apply -f app`, `kubectl port-forward svc/django-app-svc 8000:8001`

- Deploy fluentd as s demonset `kubectl apply -f fluentd-config.yaml`, `kubectl apply -f fluentd-demonset.yaml`


## Screenshoot

After setup create index patterns, 
`fluentd-k8s`(container logs pushed by demonset) and 
`django-k8s` (pushed from fluentd sidecar).

![img1](https://raw.githubusercontent.com/neeraj9194/efk-stack/master/docs/img1.png)


![img1](https://raw.githubusercontent.com/neeraj9194/efk-stack/master/docs/img2.png)