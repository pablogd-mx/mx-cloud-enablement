### BUILD Clusters

You can refer to any of our quickstarts:
For Azure: https://github.com/pablogd-mx/azure-quickstart

For AWS: https://github.com/ssahadevan-mendix/aws-mendix-quickstart


## Check Pod allocation per node

 
 ``` 
 kubens quickstart-connected
 kubectl get pods -o wide

```

## Scale Application to 2 replicas ()

## Test: Bring down all deployments in one node
### Scale down all deployments

 ``` 
 for i in $(k get deploy | awk '{print $1}' | egrep -Ev 'NAME|worker');do
kubectl scale deploy $i --replicas=0
done

```

### Check traffic to Mendix APP URL
```
URL=<TODO> - get from ingress output (kubectl get ing -n mendixns)

for i in $( seq 1 60); do
curl --head -X GET $URL | head -1
sleep 2
done
```


### Scale up all deployments

``` 
 for i in $(k get deploy | awk '{print $1}' | egrep -Ev 'NAME|worker');do
kubectl scale deploy $i --replicas=1
done

```

### Check logs of each POD



