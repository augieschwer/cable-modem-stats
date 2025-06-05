# cable-modem-stats
Retrieves stats from the Arris SB8200 cable modem and sends to InfluxDB for use with Graphana.

This project is based off of this previous work:
* https://github.com/sarabveer/cable-modem-stats
* https://github.com/andrewfraley/arris_cable_modem_stats
* https://github.com/billimek/SB6183-stats-for-influxdb
* https://github.com/t-mart/ispee

## Setup

### Minikube

https://minikube.sigs.k8s.io/docs/start/

### InfluxDB

https://docs.influxdata.com/platform/install-and-deploy/deploying/kubernetes/

```
helm repo add influxdata https://helm.influxdata.com/
helm repo update
helm install myinfluxdb influxdata/influxdb2
```

### Graphana

https://github.com/grafana/helm-charts/blob/main/charts/grafana/README.md

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install mygraphana grafana/grafana
```

#### Post-install notes

1. Get your 'admin' user password by running
```
kubectl get secret --namespace default mygraphana-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:
`mygraphana-grafana.default.svc.cluster.local` Get the Grafana URL to visit by running these commands in the same shell:
```
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=mygraphana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace default port-forward $POD_NAME 3000
```
3. Login with the password from step 1 and the username: admin

## Deploy

```
kubectl apply -f ./arris-stats-deployment.yaml
```

## Testing

Use the debug configuration to build an image with debugging enabled

```
docker build -t augie/arris_cable_modem_stats:debug -f arris-stats-debug-dockerfile .
docker run -d augie/arris_cable_modem_stats:debug
```

Spoof the SB8200 connection status page for testing

```
docker build -t augie/sb8200_spoof -f ./nginx-dockerfile .
docker run -d -p 8080:80 augie/sb8200_spoof
```

Now you can test against the URL: http://localhost:8080/cmconnectionstatus.html
