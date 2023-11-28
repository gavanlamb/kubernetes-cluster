# Certificate Manager

1. Add the Helm repository
   ```shell
   helm repo add jetstack https://charts.jetstack.io
   ```

2. Update your local Helm chart repository cache
   ```shell
   helm repo update
   ```

3. Install Cert-Manager
   ```shell
   helm upgrade --install \
   cert-manager jetstack/cert-manager \
   --namespace cert-manager \
   --create-namespace \
   --version v1.13.2 \
   --set installCRDs=true \
   --set prometheus.enabled=true \
   --set webhook.timeoutSeconds=4 \
   --set global.logLevel=5
   ```

4. Configure a Let's Encrypt Issuer
   ```shell
   kubectl create -f manifest.yaml
   ```
