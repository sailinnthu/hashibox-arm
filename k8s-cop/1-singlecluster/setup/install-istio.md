#!/usr/bin/env bash

# export ISTIO_VERSION=1.17.5
# curl -L https://istio.io/downloadIstio | ISTIO_VERSION=${ISTIO_VERSION} sh -
# sudo cp istio-${ISTIO_VERSION}/bin/istioctl /usr/local/bin

kubectl apply -f ../sample-apps/
kubectl apply -f ../istio-ingress/

# sleep 1
istioctl install --set profile=demo -y

export GATEWAY_IP=$(kubectl get svc -n istio-system istio-ingressgateway -o jsonpath="{.status.loadBalancer.ingress[0].ip}")
export INGRESS_PORT=80
export SECURE_INGRESS_PORT=443

curl -H "Host: hashisai.io" http://$GATEWAY_IP:$INGRESS_PORT
curl -H "Host: hashisai.io" http://172.18.255.150
for _ in {1..100}; do curl -I -H "Host: hashisai.io" http://172.18.255.150; sleep 0.1; done

# istioctl pc listener deployment.apps/istiod -n istio-system

============================================================================================================
#### Bookinfo App
============================================================================================================
# For ARM64 TESTING
kubectl apply -f istio-1.17.5/samples/bookinfo/platform/kube/bookinfo.yaml
kubectl  apply -f istio-1.17.5/samples/bookinfo/networking/bookinfo-gateway.yaml

kubectl port-forward -n istio-system svc/istio-ingressgateway --address 0.0.0.0 8080:80

# Test via curl
for _ in {1..100}; do curl -I http://172.18.255.150/productpage; sleep 0.1; done

# Go to Browser
http://192.168.56.85:8080/productpage 
