## Author: Yassin OUAKKA
## Date: 2024-08-13
## Description: This readme is a docummentation for the process that i followed to create the OpenTelemetry Collector Configuration with otlphttp Exporter.
# otelcol-config
# OpenTelemetry Collector Configuration with otlphttp Exporter

This repository contains an OpenTelemetry Collector configuration that collects host metrics and exports them using the OTLPHTTP exporter. The configuration is ideal for scenarios where metrics, traces, and logs need to be sent to an external observability platform via the OTLP protocol.

## Table of Contents

- [Overview](#overview)
- [Configuration Details](#configuration-details)
  - [Receivers](#receivers)
  - [Processors](#processors)
  - [Exporters](#exporters)
  - [Service Pipelines](#service-pipelines)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Running the Collector](#running-the-collector)
- [Important note](#important-note)

## Overview

This configuration is designed to collect a wide range of host metrics, traces, and logs, and export them using the otlp over http protocol. The data is sent to a specified endpoint, which is typically a centralized observability platform like Elasticsearch APM, Jaeger, or another OTLP-compatible backend.

## Configuration Details

### Receivers

The configuration includes two receivers:

1. **`hostmetrics`:** Collects system metrics at a 10-second interval. The metrics include:
    - **CPU Metrics:**
        - `system.cpu.utilization`: CPU usage percentage.
        - `system.cpu.logical.count`: Number of logical CPU cores.
    - **Memory Metrics:**
        - `system.memory.utilization`: Memory usage percentage.
    - **Process Metrics:**
        - `process.open_file_descriptors`: Number of open file descriptors by processes.
        - `process.memory.utilization`: Memory usage by processes.
        - `process.disk.operations`: Disk operations by processes.
    - Additional scrapers for network, processes, load, disk, and filesystem metrics.

2. **`otlp`:** Receives metrics, traces, and logs over gRPC and HTTP protocols.

### Processors
Two processors are configured:

1. **`resourcedetection/system` :** Adds system-related resource attributes.
2. **`resource`:** Inserts custom attributes into the metrics data, such as service.name.
``` yaml
processors:
  resourcedetection/system:
  resource:
    attributes:
      service.name: my-service
```
### Exporters
The configuration exports data using two exporters:

1. **`otlphttp`:** Exports the metrics, traces, and logs via the otlphttp protocol to the specified endpoint. The endpoint is secured with an authorization header.
2. **`debug`:** Outputs detailed information for debugging purposes.
``` yaml
exporters:
  otlphttp:
    endpoint: "https://xxxxxxxxxxxxxxxxxxxxx.apm.us-east-2.aws.elastic-cloud.com:443"
    headers: 
          "Authorization": "Bearer xxxxxxxxxxxxxx"
  debug:
    verbosity: detailed
```
### Service Pipelines
The configuration defines three pipelines: metrics, traces, and logs.

#### Metrics Pipeline:

- Receives metrics from both hostmetrics and otlp.
- Processes the metrics with resource and resourcedetection.
- Exports the processed metrics using otlphttp and debug.

#### Traces Pipeline:

- Receives traces from otlp.
- Processes the traces with resource and resourcedetection.
- Exports the processed traces using otlphttp and debug.

#### Logs Pipeline:

- Receives logs from otlp.
- Processes the logs with resource and resourcedetection.
- Exports the processed logs using otlphttp and debug.

# Getting Started

## Prerequisites

- **OTLP-Compatible Backend:** Ensure you have access to an OTLP-compatible backend such as Elasticsearch APM, Jaeger, or any other observability platform.
- **OpenTelemetry Collector:** Install the OpenTelemetry Collector (`otelcol-contrib`) on your system.

## Running the Collector

1. **Clone the Repository:**

    ```bash
    git clone https://github.com/yassinouk/otelcol-config.git
    cd otelcol-config
    ```

2. **Run the Collector (make sure to stop the collector from running in the background or it will be a conflict):**

    ```bash
    otelcol-contrib --config config.yaml
    ```

3. **Verify Data Flow:**

    - Access your OTLP-compatible backend and verify that the metrics, traces, and logs are being received and indexed properly.
    - Use your backend's tools to create visualizations and dashboards based on the collected data.

# Important note
There is a problem in this setup, we suspect one of the following reasons:
- The configuration file is not correct. (after checking the configuration file, we found that it is correct and we could receive data by searching for it manually in kibana).
- The APM server is not translating received data to **`ECS`** correctly.