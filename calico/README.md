# Calico

## Install Calico On The Master Node

1. Create the `calico` namespace on the master node
   ```shell
   kubectl create namespace calico
   ```
2. Install Calico
   ```shell
   helm install calico projectcalico/tigera-operator --version v3.26.4 --namespace calico
   ```
3. Wait for Calico to be deployed
   ```shell
   watch kubectl get pods -n calico-system
   ``` 