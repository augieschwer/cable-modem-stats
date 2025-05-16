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
helm install myinfluxdb influxdata/influxdb2
```

## Deploy

```
kubectl apply -f ./arris-stats-deployment.yaml
```

## Testing

Spoof the SB8200 connection status page for testing

```
docker build -t augie/sb8200_spoof -f ./nginx-dockerfile .
docker run -d -p 8080:80 augie/sb8200_spoof
```

Now you can test against the URL: http://localhost:8080/cmconnectionstatus.html
