### Kubernetes Monitoring with kube-prometheus-stack 

kube-prometheus-stack is a Helm chart that deploys a complete monitoring solution for Kubernetes clusters. It includes:

![image](https://github.com/user-attachments/assets/b7a8e74e-a7b5-4509-adb2-7f3af010706d)

- **Prometheus:** For metrics collection and monitoring.
- **Grafana:** For visualization and dashboarding.
- **Alertmanager:** For handling alerts.
- **Node Exporter:** For hardware and OS metrics.
- **Kube State Metrics:** For exposing metrics about the state of Kubernetes objects.
It simplifies the setup of monitoring and alerting for Kubernetes environments.

> This project demonstrates how to set up a complete monitoring solution for a Kubernetes cluster using the kube-prometheus-stack Helm chart. The stack includes Prometheus, Grafana, Alertmanager, and Node Exporter to monitor both the cluster and the hardware.

**Prerequisites**

Before starting, ensure the following are installed:

- [Docker](https://docs.docker.com/get-docker/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/)


## Step 1: Set Up the Kind Cluster

Create a Kubernetes cluster using Kind with the name `observability`.

```bash
kind create cluster --name observability
```

Verify the cluster is running:

```bash
kubectl cluster-info --context kind-observability
```
![image](https://github.com/user-attachments/assets/16d5ea4b-1fbd-4c31-996c-ea5e4f7cf3c8)

## Step 2: Install `kube-prometheus-stack`

Add the `prometheus-community` Helm chart repository and update the chart list:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## Step 3: Deploy the Chart into the Monitoring Namespace

Create a dedicated namespace for monitoring:

```bash
kubectl create ns monitoring
```

Install the `kube-prometheus-stack` Helm chart:

```bash
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
```

## Step 4: Verify the Installation

Check that all components are up and running in the `monitoring` namespace:

```bash
kubectl get all -n monitoring
```
![image](https://github.com/user-attachments/assets/4a43e6d1-793f-4e73-bc81-05e29bf4a4c9)

### Access the Prometheus UI

Use port-forwarding to access Prometheus UI:

```bash
kubectl port-forward service/prometheus-operated -n monitoring 9090:9090
```

Open your browser and navigate to: `http://localhost:9090`

### Access the Grafana UI

To access the Grafana UI, set up port-forwarding:

```bash
kubectl port-forward service/monitoring-grafana -n monitoring 8080:80
```

Open your browser and navigate to: `http://localhost:8080`

- Default Grafana username: `admin`
- Default password: `prom-operator`

### Access the Alertmanager UI

Use port-forwarding to access Alertmanager:

```bash
kubectl port-forward service/alertmanager-operated -n monitoring 9093:9093
```

Open your browser and navigate to: `http://localhost:9093`

## Step 5: Clean Up

To remove the monitoring stack and clean up the resources:

### Uninstall Helm Chart

```bash
helm uninstall monitoring --namespace monitoring
```

### Delete the Monitoring Namespace

```bash
kubectl delete ns monitoring
```

### Delete the Kind Cluster

```bash
kind delete cluster --name observability
```

