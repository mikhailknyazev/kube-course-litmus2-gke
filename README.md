## Litmus 2.x resources for "Configuring Kubernetes for Reliability with LitmusChaos"

This repo consists of resources that were used while carrying out the Litmus 2.x demonstration 
in this [video tutorial](https://www.youtube.com/watch?v=zu-hUJ_myVw). They are referenced as part 
of instructions to replicate the demo environment. Also provided are some basic details about the 
**bank-of-anthos** & **podtato-kill-with-http-probe** chaos workflows described therein. 

## Prerequisites

- Create a multi-node (preferably, 3-node) [GKE cluster](https://cloud.google.com/kubernetes-engine) with compute instance type: Ubuntu with Docker `ubuntu`. Configure [cluster access for kubectl](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl). 

## Demo Environment Configuration 

The following steps provide the testbed configuration instructions.

### Step-1: Application Deployment

- Deploy the test [applications](./applications) that will be subjected to chaos 

### Step-2: Install LitmusChaos

- Install the [LitmusChaos 2.x](https://litmusdocs-beta.netlify.app/docs/next/getting-started/installation#installation) control plane (chaos center) & local cluster-mode chaos (self) agent 

  **Note**: Update your gcp firewall rules to allow traffic to/from the litmusportal server nodeport to ensure successful
  functioning of the chaos (self) agent. 

### Step-3: Set up Observability Infra

- Set up the observability infrastructure with [kube-prometheus-stack](./monitoring/README.md)

### Step-4: Begin Monitoring Litmus & Application Metrics

- Deploy [blackbox exporter](./monitoring/blackbox-exporter.yaml) to track the podtato-head service's operational 
  characteristics

- Create servicemonitor custom resources mapped to the [chaos exporter](./monitoring/servicemonitor-chaos-exporter.yaml) and [blackbox exporter](./monitoring/servicemonitor-blackbox-exporter.yaml)

- Add the newly created servicemonitors to the [prometheus CR](./monitoring/prometheus-cr.yaml) instance to & apply to start 
  scraping the metrics

- Launch the chaos-instrumented [dashboard](./monitoring/podtato-head-dashboard.json) on Grafana to visualize service metrics

## Chaos Usecases

This section describes the intent & functioning behind the two sample chaos workflows used in the demonstration.

### Prerequisites

- **Period**: [0m0s-5m22s](https://www.youtube.com/watch?v=zu-hUJ_myVw)

- **Objectives**: 
  - Introduction to the LitmusChaos control plane (chaos center, viz litmus portal) 
  - Feature Overview

### Bank of Anthos BlackHole Chaos Workflow

- **Period**: [5m23s-11m20s](https://youtu.be/zu-hUJ_myVw?t=323)

- **Objectives**: 
  - Creation of a chaos workflow by selecting & tuning an experiment from the integrated chaoshub
  - Execution & visualization of workflow progress 
  - Examination of experiment logs & chaosresults

- **Usecase**: The workflow injects 100% [network packet loss](https://litmuschaos.github.io/litmus/experiments/categories/pods/pod-network-chaos/) in the balancereader pod, causing a degraded user experience and a semi-operational/faulty e-banking app. 

- **Possible Mitigation/Resilience Fix**: Configure services with liveness probes/health-checks that call out accessibility errors(by killing/crashing the containers) with additional replicas of the microservice at hand to serve requests. Further fixes
could involve the inclusion of middleware that can re-route request to replicas on other nodes/geo-locations based on (degraded) perf characteristics of the service. 

### Podtato-Head Pod Kill Chaos Workflow 

- **Period**: [11m21s-16:41s](https://youtu.be/zu-hUJ_myVw?t=681) 

- **Objectives**: 
  - Creation of chaos workflow from a pre-existing template 
  - Steady-state hypothesis validation through chaos duration using Litmus [HTTP Probe](https://litmuschaos.github.io/litmus/experiments/chaos-resources/probes/httpProbe/)
  - Visualization of chaos impact/manual SLO checks via chaos interleaved grafana dashboards
  - Examination of experiment logs & chaosresults (with probe success/failures)

- **Usecase**: The workflow injects a [pod kill/deletion](https://litmuschaos.github.io/litmus/experiments/categories/pods/pod-delete/) fault on a single-replica podtato-head application causing the availability percentage to drop below the set threshold & also violating access latency limits until the pod is rescheduled and initialized.

- **Possible Mitigation/Resilience Fix**: Follow deployment best practices with multi-replica deployments so that kube-proxy can route requests to other live end-points. 

## Conclusion 

To learn more about LitmusChaos 2.x, refer to the [documentation](https://litmusdocs-beta.netlify.app/docs/next/introduction/what-is-litmus). 


