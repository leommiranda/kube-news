## Setup local

O repositório está todo configurado com o CI/CD do GitHub Actions. Logo, não é necessário rodar os seguintes comandos manualmente. O objetivo deste guia é mostrar como se roda o repositório em um computador pessoal (não no servidor da produção).

#### Como atualizar a imagem docker

```
# ir para a pasta "src" deste repositório
cd kube-news/src

# build a imagem docker
docker build . -t leommiranda/kube-news:[numero_da_versao]

# fazer login para usar o dockerhub
docker login

# subir a imagem docker para o dockerhub
docker push leommiranda/kube-news:[numero_da_versao]

# atualizar a latest
docker tag leommiranda/kube-news:[numero_da_versao] leommiranda/kube-news:latest
docker push leommiranda/kube-news:latest
```


#### Baixar e rodar

```
# clonar o repositório
git clone https://github.com/leommiranda/kube-news

# ir para a pasta "k8s" deste repositório
cd kube-news/k8s

# criar cluster com k3d
k3d cluster create mycluster --servers 1 --agents 1 -p "30000:30000@loadbalancer"

# caso esteja no computador pessoal, substituir a última linha do arquivo "deployment.yaml"
# substitua "type: LoadBalancer" por "type: NodePort"

# rodar
kubectl apply -f deployment.yaml --kubeconfig=/my/path/to/kube_config.yaml

# conferir até estar rodando (dica: usar watch)
kubectl get all --kubeconfig=/my/path/to/kube_config.yaml

# acessar localhost:30000 pelo browser
```


#### Como criar certificado tls
Mais detalhes em: https://kubernetes.github.io/ingress-nginx/user-guide/tls/

```
# substitua a string "134.209.128.120.nip.io" pelo domínio da aplicação e "kubenews-ca" por um nome adequado para a aplicação
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout kubenews-ca.key -out kubenews-ca.crt -subj "/CN=134.209.128.120.nip.io/O=134.209.128.120.nip.io"
```

#### Como subir certificado tls para o cluster k8s

```
# substitua a string "kubenews-ca" por um nome adequado para a aplicação
kubectl create secret tls kubenews-ca-tls --key kubenews-ca.key --cert kubenews-ca.crt --kubeconfig=/my/path/to/kube_config.yaml
```

#### Como rodar o cluster issuer

```
# ir para a pasta inicial deste repositório
cd kube-news

# rodar
kubectl apply -f k8s/clusterissuer.yaml --kubeconfig=/my/path/to/kube_config.yaml
```



#### Como instalar e rodar o ingress controller da nginx no cluster (sem helm)
Mais detalhes em: https://kubernetes.github.io/ingress-nginx/deploy/

```
# instalar (lembrar de talvez atualizar a versão do nginx ingress controller, a versão usada neste comando é a v1.3.0)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml --kubeconfig=/my/path/to/kube_config.yaml

# ir para a pasta inicial deste repositório
cd kube-news

# rodar
kubectl apply -f k8s/ingress.yaml --kubeconfig=/my/path/to/kube_config.yaml
```

