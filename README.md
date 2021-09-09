# EFK Stack

Elasticsearch, Fluentd, and Kibana (EFK) stack is a centralized logging solution.


## Setup

- Create namespace `kubectl create namespace efk-monitoring`
- Install elastic search using helm
`helm repo add elastic https://helm.elastic.co`

`helm repo update`


`helm install elasticsearch elastic/elasticsearch -n efk-monitoring --set persistence.enabled=false,replicas=1`

Setting persistence false for local.

- 

