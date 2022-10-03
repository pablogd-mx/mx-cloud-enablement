
### BUILD Clusters
## Azure AKS
https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler
# Create AKS cluster with Autoscaler on
az group create --name myResourceGroup --location mylocation

az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3


# Update AKS cluster to enable autoscaler
az aks update \
  --resource-group myResourceGroup \
  --name myAKScluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3


az aks update \
  --resource-group pablo-rg-weu \
  --name pablo-aks \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3


# Set the cluster autoscaler profile on an existing AKS cluster

## all values are found here: https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler

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

eksctl create cluster --asg-access 
#-->  This flag also sets k8s.io/cluster-autoscaler/enabled and k8s.io/cluster-autoscaler/<clusterName> tags, so nodegroup discovery should work.

nodeGroups:
  - name: ng1-public
    iam:
      withAddonPolicies:
        autoScaler: true


eksctl create cluster --asg-access -f cluster_autoscaler.yaml


# Install Autoscaler in AWS EKS
https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md










# Let's install Metrics Server first

https://github.com/kubernetes-sigs/metrics-server







# Mendix Horizontal Pod Autoscaler

kubectl autoscale mendixapp dbsedgju --cpu-percent=50 --min=1 --max=3
kubectl edit horizontalpodautoscaling {envname}






# VPA

VPA docs [here]("https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#install-comman

git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler

./hack/vpa-up.sh 

kubectl -n kube-system get pods | grep vpa

# Deploying an example for VPA

kubectl apply -f ../../../artifacts/mendix-vpa.yaml

verticalpodautoscaler.autoscaling.k8s.io/dbsedgju-master created

kubectl describe vpa vt9ro343-master

## Install Goldilocks
cd /tmp
git clone https://github.com/FairwindsOps/goldilocks.git
cd goldilocks/hack/manifests/

kubectl create namespace goldilocks
kubectl -n goldilocks apply -f ./controller
kubectl -n goldilocks apply -f ./dashboard


kubectl label ns default goldilocks.fairwinds.com/enabled=true
kubectl label ns default goldilocks.fairwinds.com/vpa-update-mode="off"

kubectl -n goldilocks port-forward svc/goldilocks-dashboard 80


# Generate some traffic

Let's deploy a simple traffic generator pod

```
cd /Users/Pablo.Diaz/Documents/Mx4PC/Mx4PC-TrainingWorkshops/HA-CloudEnablementSession/Scalability/artifacts
kubectl apply -f traffic-generator.yaml

# get a terminal to the traffic-generator
kubectl exec -it traffic-generator sh

# install wrk
apk add --no-cache wrk
wrk -c 5 -t 5 -d 99999 -H "Connection: Close"
# simulate some load
wrk -c 5 -t 5 -d 99999 -H "Connection: Close" http://mendix-vpa

#you can scale to pods manually and see roughly 6-7 pods will satisfy resource requests.
kubectl scale deploy/mendix-vpa --replicas 2


