Cluster example with 1024 Mb Heap Memory and 60Gi persistent storage, 
if you need more than please replace 1024 to required value, but *not more than 20% of available node RAM*

Create 2 pools:

Pool #1 with 1 persistent node
Pool #2 with 2 preemptible nodes

1. Auth kubectl to cluster

2. Prepare tiller

```bash
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```

3. Deploy cluster

```bash

helm init
git clone -b elastic5-configurable http://github.com/maksimru/helm-elasticsearch.git helm-elasticsearch
helm install \
--set client.heapMemory=1024m,client.resources.requests.memory=1024Mi \
--set data.heapMemory=1024m,data.resources.requests.memory=1024Mi,data.stateful.size=60Gi \
--set master.heapMemory=1024m,master.resources.requests.memory=1024Mi,data.stateful.size=60Gi \
./helm-elasticsearch 

```

4. Access to kibana

http://KIBANA_SVC:9200

5. Cluster status

http://ELASTICSEARCH_SVC:9200/_cat/nodes?v
http://ELASTICSEARCH_SVC:9200/_cluster/health?pretty

6. Put under internal load balancer (GKE only)

--set kibana.service.annotations.cloud\\.google\\.com\/load-balancer-type="Internal"
--set service.annotations.cloud\\.google\\.com\/load-balancer-type="Internal"