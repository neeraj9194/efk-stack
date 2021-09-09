# EFK Stack

Elasticsearch, Fluentd, and Kibana (EFK) stack is a centralized logging solution.

![img1](https://platform9.com/wp-content/uploads/2020/07/elastic-kibana.png)

## Setup

- Create namespace `kubectl create namespace efk-monitoring`
- Install elastic search using helm
`helm repo add elastic https://helm.elastic.co`

`helm repo update`

`helm install elasticsearch elastic/elasticsearch -f ./values.yaml`
`helm install elasticsearch elastic/elasticsearch -n efk-monitoring --set persistence.enabled=false,replicas=1`

- `helm test elasticsearch`

- `helm install kibana elastic/kibana --set env.ELASTICSEARCH_URL=http://elasticsearch-master:9200`

- `kubectl port-forward svc/elasticsearch-master 9200`

- `helm install metricbeat elastic/metricbeat`

- `kubectl port-forward deployment/kibana-kibana 5601`


