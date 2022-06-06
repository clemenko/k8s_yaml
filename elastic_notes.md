# Elastic Notes

notes for the rodeo...

```bash
kubectl create -f https://download.elastic.co/downloads/eck/2.2.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/2.2.0/operator.yaml

kubectl create ns elastic

cat <<EOF | kubectl apply -f -
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: quickstart
  namespace: elastic
spec:
  version: 8.2.2
  nodeSets:
  - name: default
    count: 1
    config:
      node.store.allow_mmap: false
---
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: quickstart
  namespace: elastic
spec:
  version: 8.2.2
  count: 1
  elasticsearchRef:
    name: quickstart
---
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRouteTCP
metadata:
  name: kibana
  namespace: elastic
spec:
  entryPoints:
    - tcp
  routes:
    - match: HostSNI(\`kibana.rfed.io\`)
      services:
        - name: quickstart-kb-http
          port: 5601
  tls:
    passthrough: true
EOF

kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' -n elastic| base64 --decode; echo 

```


