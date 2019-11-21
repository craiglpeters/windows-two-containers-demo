# windows-two-containers-demo
Quick demo using AKS Engine to create a cluster with Windows nodes and deploy a pod with two Windows containers
Also see the [slides](https://www.slideshare.net/CraigPeters6/windows-containers-on-kubernetes) shared for the Kubernetes Colorado meetup

Summary:
1. Follow the steps in the [AKS Engine Quickstart](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) using the kubernetes.json in this repo
2. Apply the two-containers.yaml in this repo to the cluster (borrowed from the aks-engine [windows-and-kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/windows-and-kubernetes.md#multiple-containers-in-a-pod) examples

Step by step from my Mac
```bash
$ brew upgrade kubectl
$ brew install Azure/aks-engine/aks-engine
$ az login
$ export RESOURCEGROUP=akse-windows
$ export LOCATION=southcentralus
$ az group create –n $RESOURCEGROUP –l $LOCATION
$ export RESOURCEGROUPID=$(az group show --resource-group $RESOURCEGROUP --query id --output tsv)
$ export SERVICEPRINCIPAL=$(az ad sp create-for-rbac --role="Contributor" --scopes=$RESOURCEGROUPID --output json)
$ curl -L https://raw.githubusercontent.com/Azure/aks-engine/master/examples/windows/kubernetes.json -o kubernetes.json
# edit dnsPrefix, adminPassword, keyData, clientId, secret 
$ code kubernetes.json
$ aks-engine generate kubernetes.json
$ az group deployment create –g $RESOURCEGROUP –-template-file "./_output/wink8s1/azuredeploy.json" –-parameters "./_output/wink8s1/azuredeploy.parameters.json" > az-group-deployment.out
$ export KUBECONFIG=$(PWD)/_output/wink8s1/kubeconfig/kubeconfig.southcentralus.json
$ kubectl get nodes –o wide
$ kubectl apply –f two-containers.yaml
$ kubectl get pods
$ kubectl port-forward two-containers-… 7000:80
```

Getting logs
```bash
$ kubectl get pods
$ kubectl exec -ti two-containers-… powershell
```
```Powershell
PS C:\> Dir .\inetpub\logs\LogFiles\W3SVC1\
PS C:\> Get-Content .\inetpub\logs\LogFiles\W3SVC1\u_ex191105.log -Tail 2 #replace log file name
```
