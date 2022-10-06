### BUILD Clusters

You can refer to any of our quickstarts:
For Azure: https://github.com/pablogd-mx/azure-quickstart

For AWS: https://github.com/ssahadevan-mendix/aws-mendix-quickstart

## Enabling Autoscaling
### Azure AKS
https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler

### Create AKS cluster with Autoscaler on
``` 
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
  ```
#### All cluster autoscaler parameters are here:
https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler


# Update AKS cluster to enable autoscaler
```
az aks update \
  --resource-group myResourceGroup \
  --name myAKScluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```
```
az aks update \
  --resource-group pablo-rg-weu \
  --name pablo-aks \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## Set the cluster autoscaler profile on an existing AKS cluster

``` 
az aks update \
  --resource-group pablo-rg-weu \
  --name pablo-aks \
  --cluster-autoscaler-profile scan-interval=10s
```

```
az aks update \
  --resource-group  \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=10s
```

## All values are found here:
 https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler

### Updating autoscaler profile

az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s

az aks update \
  --resource-group pablo-rg-weu \
  --name pablo-aks \
  --cluster-autoscaler-profile scan-interval=30s






## Amazon EKS

### Install Autoscaler in AWS EKS
https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md

### Update Existing EKS cluster with Autoscaler.

```
eksctl create cluster --asg-access 
#-->  This flag also sets k8s.io/cluster-autoscaler/enabled and k8s.io/cluster-autoscaler/<clusterName> tags, so nodegroup discovery should work.

nodeGroups:
  - name: ng1-public
    iam:
      withAddonPolicies:
        autoScaler: true
```
```
eksctl create cluster --asg-access -f cluster_autoscaler.yaml

```


### Let's install Metrics Server first

https://github.com/kubernetes-sigs/metrics-server

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml



# Mendix Horizontal Pod Autoscaler

``` 
kubectl autoscale mendixapp [mendixappname] --cpu-percent=50 --min=1 --max=3

kubectl edit horizontalpodautoscaling {envname}
``` 

## Let's deploy a simple traffic generator pod
### Generate some traffic

```
cd /Users/Pablo.Diaz/Documents/Mx4PC/Mx4PC-TrainingWorkshops/HA-CloudEnablementSession/Scalability/artifacts
kubectl apply -f artifacts/traffic-generator.yaml
```

### Get a terminal to the traffic-generator
```
kubectl exec -it traffic-generator sh
```

### install wrk
```
apk add --no-cache wrk
wrk -c 5 -t 5 -d 99999 -H "Connection: Close"
# simulate some load
wrk -c 1000 -t 10 -d 200 -H "Connection: Close" http://vt9ro343:8080
```
### You can scale to pods manually and see roughly 6-7 pods will satisfy resource requests.
kubectl scale deploy/mendix-vpa --replicas 2



# VPA

VPA docs [here]("https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#install-comman

## Installing VPA
```
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler

./hack/vpa-up.sh 

kubectl -n kube-system get pods | grep vpa

# Deploying an example for VPA

kubectl apply -f ./artifacts/mendix-vpa.yaml

kubectl describe vpa mendix-vpa

## Install Goldilocks
cd /tmp
git clone https://github.com/FairwindsOps/goldilocks.git
cd goldilocks/hack/manifests/

kubectl create namespace goldilocks
kubectl -n goldilocks apply -f ./controller
kubectl -n goldilocks apply -f ./dashboard


kubectl label ns default goldilocks.fairwinds.com/enabled=true
kubectl label ns default goldilocks.fairwinds.com/vpa-update-mode="off"

kubectl port-forward svc/goldilocks-dashboard 8080:80





