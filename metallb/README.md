# MetalLB

## Install MetalLB

1. Enable strict ARP mode
   ```shell
   # actually apply the changes, returns nonzero returncode on errors only
   kubectl get configmap kube-proxy -n kube-system -o yaml | \
   sed -e "s/strictARP: false/strictARP: true/" | \
   kubectl apply -f - -n kube-system
   ```

2. Install MetalLB with Helm
   ```shell
   helm repo add metallb https://metallb.github.io/metallb
   helm install metallb metallb/metallb
   ```

3. Add Resources
   ```shell
   kubectl apply -f manifest.yaml
   ```
