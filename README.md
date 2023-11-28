# Kubernetes Cluster

1. Disable swap on each node
   ```shell
   sudo swapoff -
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   ```
   
2. Set up hostnames on each node
   ```shell
   sudo hostnamectl set-hostname "dh-ubuntu-0"
   exec bash
   ```

3. Update the `/etc/hosts` File for Hostname Resolution on each node
   ```shell
   192.168.50.30 dh-ubuntu-0
   192.168.50.31 dh-ubuntu-1
   192.168.50.32 dh-ubuntu-2
   ```
4. Set up the IPV4 bridge on each node
   ```shell
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF
   
   sudo modprobe overlay
   sudo modprobe br_netfilter
   
   # sysctl params required by setup, params persist across reboots
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.bridge.bridge-nf-call-iptables  = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward                 = 1
   EOF
   
   # Apply sysctl params without reboot
   sudo sysctl --system
   ```
   
5. Install kubelet, kubeadm, and kubectl on each node
   ```shell
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl
   sudo mkdir /etc/apt/keyrings
   curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
   echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   sudo apt-get update
   sudo apt install -y kubelet=1.26.5-00 kubeadm=1.26.5-00 kubectl=1.26.5-00
   ```

6. Install Docker on each node
   ```shell
   sudo apt install docker.io
   sudo mkdir /etc/containerd
   sudo sh -c "containerd config default > /etc/containerd/config.toml"
   sudo sed -i 's/ SystemdCgroup = false/ SystemdCgroup = true/' /etc/containerd/config.toml
   sudo systemctl restart containerd.service
   sudo systemctl restart kubelet.service
   sudo systemctl enable kubelet.service
   ```

7. Initialize the Kubernetes cluster on the master node
   ```shell
   sudo kubeadm config images pull
   sudo kubeadm init --pod-network-cidr=10.10.0.0/16
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

8. Configure kubectl and Calico on the master node
   ```shell
   kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
   curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml -O
   sed -i 's/cidr: 192\.168\.0\.0\/16/cidr: 10.10.0.0\/16/g' custom-resources.yaml
   kubectl create -f custom-resources.yaml
   ```

9. Add worker nodes to the cluster
   ```shell
   sudo kubeadm join <MASTER_NODE_IP>:<API_SERVER_PORT> --token <TOKEN> --discovery-token-ca-cert-hash <CERTIFICATE_HASH>
   ```
   
10. Verify the cluster and test
   ```shell
   kubectl get no
   kubectl get po -A
   ```

11. Install Helm [README.md](./helm/README.md)

12. Install Calico [README.md](./calico/README.md)

13. Install NFS [README.md](./nfs/README.md)

14. Install MetalLB [README.md](./metallb/README.md)

15. Install NGinX [README.md](./nginx/README.md)

16. Install cert-manager [README.md](./cert-manager/README.md)

17. Install portainer [README.md](./portainer/README.md)



