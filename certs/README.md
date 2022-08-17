openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout kubenews-ca.key -out kubenews-ca.crt -subj "/CN=134.209.128.120.nip.io/O=134.209.128.120.nip.io"


kubectl create secret tls kubenews-ca-tls --key kubenews-ca.key --cert kubenews-ca.crt --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml


For instance, if you have a TLS secret foo-tls in the default namespace, add --default-ssl-certificate=default/foo-tls in the nginx-controller deployment.


--default-ssl-certificate=default/kubenews-ca-tls
nginx-controller deployment


--enable-ssl-passthrough
