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


![img2](https://raw.githubusercontent.com/neeraj9194/efk-stack/master/docs/img2.png)



## Fluentd vs Fluentbit

- Fluent Bit was designed for high performance and comes with a super light footprint.

![img3](https://logz.io/wp-content/uploads/2018/06/compare-chart.png)

Fluent Bit acts as a collector and forwarder and was designed with performance in mind, as described above. 

Fluentd was designed to handle heavy throughput — aggregating from multiple inputs, processing data and routing to different outputs. 

Fluent Bit is not as pluggable and flexible as Fluentd, which can be integrated with a much larger amount of input and output sources.   

Fluent Bit ships with native support for metric collection from the environment they are deployed on.


In Kubernetes for example, Fluent Bit would be deployed per node as a daemonset, collecting and forwarding data to a Fluentd instance deployed per cluster and acting as an aggregator — processing the data and routing it to different sources based on tags.

![img4](https://dytvr9ot2sszz.cloudfront.net/wp-content/uploads/2018/06/kuberbetes-monitoring-arch-1.jpg)

