## Deploying with custom values

- create a custom `values.yaml` file

- then, deploy using it with
```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo --values ./helm/my-values.yaml
```

- if you want to test before what is generated, you can use
```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo --dry-run --values ./helm/my-values.yaml
```

- if you want to upgrade existing release
```shell
helm upgrade my-otel-demo open-telemetry/opentelemetry-demo --values ./helm/my-values.yaml
```

### Export telemetry data to Lightstep

- add yaml below to your custom `values.yaml` file
```yaml
opentelemetry-collector:
  config:
    exporters:
      otlp/lightstep:
        endpoint: ingest.lightstep.com:443
        headers:
          "lightstep-access-token": "<YOUR_LIGHTSTEP_ACCESS_TOKEN>"

    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: [batch]
          exporters: [logging, otlp/lightstep]
        metrics:
          receivers: [otlp]
          processors: [batch]
          exporters: [logging, otlp/lightstep]
```

### Add GKE/GCP cloud attributes
- add or update yaml below to your custom `values.yaml` file
```yaml
opentelemetry-collector:
  config:
    processors:
      resourcedetection/gke:
        detectors: [env, gke, gcp]
        timeout: 2s
        override: false
```

- don't forget to add this processor to your pipelines, example:
```yaml
    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: [batch, resourcedetection/gke]
          exporters: [logging, otlp/lightstep]
        metrics:
          receivers: [otlp]
          processors: [batch, resourcedetection/gke]
          exporters: [logging, otlp/lightstep]
```

> **_NOTE:_**  More examples on adding resource attributes available [here](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/resourcedetectionprocessor)


## Adding Kubernetes metrics

- create role to retrieve metrics from API in your kubernetes cluster
```shell
kubectl apply -f ./helm/otel-cluster-role.yaml
```

- in file `./helm/otel-cluster-role-assign.yaml`, replace name of the service account with the one used by your OpenTelemetry Collector
  - by default, it is `{{ release.name}}-otelcol`, like `my-otel-demo-otelcol`

- Assign role to the service account used by your collector
```shell
kubectl apply -f ./helm/otel-cluster-role-assign.yaml
```

- Add the k8s metrics receiver to your collector configuration
    - add or update yaml below to your custom `values.yaml` file
    ```yaml
    opentelemetry-collector:
      config:
        receivers:
          k8s_cluster:
            collection_interval: 10s        
    ```

    - don't forget to add this processor to your `metrics` pipeline(s), example:
    ```yaml
        service:
          pipelines:
            metrics:
              receivers: [otlp]
              processors: [batch, resourcedetection/gke]
              exporters: [logging, otlp/lightstep]
    ```

- Upgrade your deployment
```shell
helm upgrade my-otel-demo open-telemetry/opentelemetry-demo --values ./helm/my-values.yaml
```

> **_NOTE:_**  See more details on receiver [here](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/k8sclusterreceiver)
