# Day 1 do curso Descomplicando Kubernetes

No inicio foram visto alguns conceitos cruciais para começar a criar clusters Kubernetes, como: Distro Linux, sites importantes, Container Engine, Conteiner Runtime, OCI, o que é container, o que é kubernetes, control plane, workers, API Server, etcd, Scheduler, Controller Manager, Kubelet, Kube-proxy, portas dos serviços, pods, deployment, replicasets e services.

Posteriormente iniciamos a criação de um cluster de teste usando Kind:
- Instalação:

```curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.14.0/kind-linux-amd64```

`chmod +x ./kind`

`sudo mv ./kind /usr/local/bin/kind`


E também com a instalação do kubectl:

- Instalação:

```curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl ```

`chmod +x ./kubectl`

`sudo mv ./kubectl /usr/local/bin/kubectl`

`kubectl version --client`

Para criar o cluster Kind é só usar o comando `kind create cluster`, e verificar se o cluster foi criado usando `kind get clusters`. Para listar os nodes do cluster se usa `kubectl get nodes`.

O comando anterior de criação de cluster só sobe 1 node, porém kind também permite a criação de vários nodes separados na mesma máquina. Para isso é necessário criar um arquivo de configuração .yaml:

    kind: Cluster
    apiVersion: kind.x-k8s.io/v1alpha4
    nodes:
      - role: control-plane
      - role: worker
      - role: worker
    EOF

No arquivo acima são criados 3 nodes, sendo 1 control-plane e 2 workers.

Para criar usando o arquivo de configuração, executamos `kind create cluster --name kind-multinodes --config $HOME/kind-3nodes.yaml` onde `$HOME/kind-3nodes.yaml` é o nosso arquivo de configuração.

Em seguida podemos verificar os principais recursos dentro do cluster usando:

`kubectl get namespaces`

`kubectl get pod -n kube-system` Esse comando retorna pods do namespace kube-system, que é onde ficam os pods de sistema do Kubernetes.

`kubectl get pods -A` Esse comando retorna pods de todos os namespaces.

`kubectl get pods -A -o wide` Esse comando retorna todos os pods e mostra mais detalhes sobre eles.

Para executar um pod do nginx a partir de uma imagem, podemos usar o comando `kubectl run nginx --image nginx`, porém é pouco usual visto que é ideal utilizar deployments e conceitos GitOps.

Para remover o pod podemos usar o comando `kubectl delete pod nginx`, por exemplo.

Uma forma interessante de utilizar o dry-run, é gerar um arquivo .yaml para subir uma imagem usando o comando `kubectl run meu-nginx --image nginx --dry-run=client -o yaml > pod-template.yaml` e em seguida podemos fazer o deploy usando o arquivo .yaml usando `kubectl apply -f pod-template.yaml`.

Uma forma de expôr os recursos para o exterior é criando um serviço `kubectl expose pod nginx`. Isso irá especificamente criar um service do tipo ClusterIp, na porta do pod informado.

Um comando extra que é interessante é o `kubectl exec -ti giropops -- bash` que acessa o bash do pod. É possível enviar qualquer comando para o pod após `--`.
