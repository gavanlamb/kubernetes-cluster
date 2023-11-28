# NGinX

## Install MetalLB
1. Install Nginx with Helm
   ```shell
   helm upgrade --install --create-namespace \
   -n portainer portainer portainer/portainer \
   --set enterpriseEdition.enabled=true \
   --set service.type=ClusterIP \
   --set tls.force=true \
   --set ingress.enabled=true \
   --set ingress.ingressClassName=nginx \
   --set ingress.annotations."nginx\.ingress\.kubernetes\.io/backend-protocol"=HTTPS \
   --set ingress.annotations."cert-manager\.io/cluster-issuer"=letsencrypt-production \
   --set ingress.tls[0].hosts[0]=portainer.gavanlamb.com \
   --set ingress.tls[0].secretName=tls-portainer-gavanlamb-com \
   --set ingress.hosts[0].host=portainer.gavanlamb.com \
   --set ingress.hosts[0].paths[0].path="/" \
   --set persistence.storageClass=nfs
   ```
