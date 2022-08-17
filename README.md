kubectl create secret generic db-secret \
    --from-literal=POSTGRES_DB="kubenews_database" \
    --from-literal=POSTGRES_PASSWORD="kubenews_password123" \
    --from-literal=POSTGRES_USER="kubenews_user" \
    --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml

## instalar ingress controller

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml

kubectl get all -n ingress-nginx --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml


kubectl apply -f k8s/ingress.yaml --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml

kubectl get ingress --kubeconfig=/home/leommiranda/Desktop/iniciativa_kubernetes/kube-news-iac/kube_config.yaml



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
kubectl apply -f deployment.yaml

# conferir até estar rodando (dica: usar watch)
kubectl get all

# acessar localhost:30000 pelo browser
```
