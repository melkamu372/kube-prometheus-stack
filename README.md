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
![p-1](https://github.com/user-attachments/assets/fdb6e796-4f86-441e-8670-9c32cc97d5b9)


## Step 2: Install `kube-prometheus-stack`

Add the `prometheus-community` Helm chart repository and update the chart list:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
![p-2](https://github.com/user-attachments/assets/5f616e1f-f61e-4260-9805-f17caf888714)


## Step 3: Deploy the Chart into the Monitoring Namespace

Create a dedicated namespace for monitoring:

```bash
kubectl create ns monitoring
```

Install the `kube-prometheus-stack` Helm chart:

```bash
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
```
![p-3](https://github.com/user-attachments/assets/5e231e9f-6438-4f4e-b83a-c01dfa0401bb)


## Step 4: Verify the Installation

Check that all components are up and running in the `monitoring` namespace:

```bash
kubectl get all -n monitoring
```

![p-4](https://github.com/user-attachments/assets/77913d65-37ee-4ee9-b4a4-cc908016df16)

![p-5](https://github.com/user-attachments/assets/2c6d1e1c-b872-41d2-a9e3-2a8588c70e70)

### Access the Prometheus UI

Use port-forwarding to access Prometheus UI:

```bash
kubectl port-forward service/prometheus-operated -n monitoring 9090:9090
```
![p-6](https://github.com/user-attachments/assets/ebc839d3-c614-4979-8e2a-63cf72469a0d)


Open your browser and navigate to: `http://localhost:9090`
![image](https://github.com/user-attachments/assets/21f481d3-cdaf-4b87-8703-bca7b4c43f67)
![image](https://github.com/user-attachments/assets/8125eb1f-a8a5-4fae-8fb4-4f8f28b94f68)


### Access the Grafana UI

To access the Grafana UI, set up port-forwarding:

```bash
kubectl port-forward service/monitoring-grafana -n monitoring 3000:80
```
![p-7](https://github.com/user-attachments/assets/facc140d-e959-4bc6-ba3b-ed3bbf36573d)

Open your browser and navigate to: `http://localhost:3000`

- Default Grafana username: `admin`
- Default password: `prom-operator`

![p-10](https://github.com/user-attachments/assets/6fca57a3-9123-4f43-bcb7-42636ef4e42f)

![image](https://github.com/user-attachments/assets/781244ae-782d-447b-876c-be92629d7c7a)
![image](https://github.com/user-attachments/assets/3876825b-d664-414e-877b-f22c34ce0ad8)
![image](https://github.com/user-attachments/assets/60a9546e-4c23-497c-adf3-4adb92b2d437)
![image](https://github.com/user-attachments/assets/52834ca8-2c08-450c-b289-9ac254124d22)

### Access the Alertmanager UI

Use port-forwarding to access Alertmanager:

```bash
kubectl port-forward service/alertmanager-operated -n monitoring 9093:9093
```
![p-8](https://github.com/user-attachments/assets/ccda40eb-e8a0-44e8-b256-db5e0a4e234d)

Open your browser and navigate to: `http://localhost:9093`

![p-11](https://github.com/user-attachments/assets/47a4fcd3-a58c-4b54-95ab-3064a5771b76)

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

