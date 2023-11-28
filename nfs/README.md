# NFS

## Setup NFS Server
1. Install nfs-kernel-server
   ```shell
   sudo apt-get install nfs-kernel-server
   ```

2. Create a directory to be used for NFS
   ```shell
   sudo mkdir -p /share/kubernetes
   sudo chown nobody:nogroup /share/kubernetes
   sudo chmod 0777 /share/kubernetes
   ```

## Install NFS Drivers In Kubernetes On The Master Nod
1. Add Helm repo 
   ```shell
   helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
   ```

2. Add Helm repo
   ```shell
   helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --version v4.5.0
   ```

3. Add Resources
   ```shell
   kubectl apply -f manifest.yaml
   ```
