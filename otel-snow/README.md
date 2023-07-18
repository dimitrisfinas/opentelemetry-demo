# DEPLOYING OPENTELEMETRY SERVICENOW LIGHTSTEP CONTAINER

This container is used for integration between Lightstep and ServiceNow by the Service Graph Connector

## PREPARE
- Go to your lightstep project
    - create an API Key with READ access (this will be used by collector to read your stream data)
    - copy your api key value in the secret file `secret.yaml`
    - create a stream with all your services (this will be used by SGC to create service and dependencies map)
    - copy your stream id, organization and project in the file `configmap.yaml`

## DEPLOY

1. Deploy the secret value
```yaml
kubectl apply -f secret.yaml
```

2. Deploy the config map
```yaml
kubectl apply -f configmap.yaml
```

3. Deploy the SGC Collector
```yaml
kubectl apply -f otel-sgc-collector.yaml
```

This file contains both definitions for: service account, service and deployment


## TEST

- Go to the url of your service `http://<IP_ASSIGNED>:55688/`
- As a result page, you should get a json file like:
```json
{"data":{"resources":[{"attributes":{"cloud.provider":"azure","cloud.region":"West-US","host.name":"productcatalogservice-6b654dbf57-zq8dt","host.type":"t3.medium","k8s.cluster.name":"k8s-cluster-2","service.name":"productcatalogservice"},"last_seen":"2023-01-25T11:52:32Z"},{"attributes":...
{"from":"recommendationservice","to":"productcatalogservice","to_operation":"/GetProducts","from_operation":"/GetRecommendations","span_count":21551,"last_seen":"2023-01-25T11:52:32Z"}]}}
```
