### ⚠️ This is a replicas of the google doc, please make changes in the google doc and then update this file accordingly.
### 📝 [Google Doc](https://docs.google.com/document/d/1K7qzbLeYF_2NW5dYGS3RYh4zg3wSdH8oFz1iXqNa7_M/edit?usp=sharing)
### Author: [Yassin OUAKKA](https://github.com/yassinouk)
### Date: 2024-08-14
### Version: 0.1.0
# OpenTelemetry (Otel) Configuration

## Receivers

### Host Metrics Receiver

The `hostmetrics` receiver is configured to collect various system metrics at specified intervals.
```yaml
hostmetrics:
  collection_interval: 10s
  scrapers:
    load:
    memory:
    cpu:
    filesystem:
    network:
    disk:
```

# Processors

## Resource Processor

The resource processor is used to apply changes to resource attributes. Below is an example configuration:

```yaml
processors:
  resource:
    attributes:
    - key: service.name
      action: upsert
      value: MyEC2Instance
    - key: service.version
      action: upsert
      value: 1.0.0
    - key: deployment.environment
      action: upsert
      value: dev
```
### Difference Between Upsert and Insert

- **Insert:** Used to add an attribute only if it does not already exist.
- **Upsert:** Similar to insert, but if the attribute exists, it will be updated with the new value.

## Resource Detection Processor
### EC2 Detector

The EC2 detector reads resource information from the EC2 instance metadata API to retrieve the following resource attributes:

- `cloud.provider` ("aws")
- `cloud.platform` ("aws_ec2")
- `cloud.account.id`
- `cloud.region`
- `cloud.availability_zone`
- `host.id`
- `host.image.id`
- `host.name`
- `host.type`

### GCP Detector

The GCP detector uses the Google Cloud Client Libraries for Go to read resource information from the metadata server and environment variables. It detects the platform on which the application is running and retrieves the appropriate attributes. The GCP detector supports different GCP platforms:

### GCE Metadata:

- `cloud.provider` ("gcp")
- `cloud.platform` ("gcp_compute_engine")
- `cloud.account.id` (project ID)
- `cloud.region` (e.g., "us-central1")
- `cloud.availability_zone` (e.g., "us-central1-c")
- `host.id` (instance ID)
- `host.name` (instance name)
- `host.type` (machine type)
- (optional) `gcp.gce.instance.hostname`
- (optional) `gcp.gce.instance.name`

### GKE Metadata:

- `cloud.provider` ("gcp")
- `cloud.platform` ("gcp_kubernetes_engine")
- `cloud.account.id` (project ID)
- `cloud.region` (only for regional GKE clusters; e.g., "us-central1")
- `cloud.availability_zone` (only for zonal GKE clusters; e.g., "us-central1-c")
- `k8s.cluster.name`
- `host.id` (instance ID)
- `host.name` (instance name; only when workload identity is disabled)