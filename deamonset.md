## Deamonset

O *daemonset* se difere do *replicaset* pelo fato de voce nao escolher quantas replicas voce vai ter. A vantagem do *daemonset* e que voce pode por os *POD* em todos os nos do cluster. O legal de se usar o *daemonset* e voce ter a garantia que um dos *PODS* vai estar rodando em pelo menos 1 dos seus nodes do cluster.

1.  Vamos criar nosso primeiro *daemonset.yml*

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: primeiro_daemonset
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
  updateStrategy:
    type: RollingUpdate
```

1.1 Para que possamos criar:

`# kubectl create -f quarto_daemonset.yml`

1.2 Vamos agora analisar a saia do comando do daemonset:

```bash
# kubectl get daemonsets.apps 
NAME               DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
quarto-daemonset   2         2         2       2            2           <none>          4s
```

1.3 Agora vamos editar e alterar a imagem do NGINX desse daemonset

```bash
# kubectl edit daemonsets.apps quarto-daemonset
daemonset.apps/quarto-daemonset edited
```

1.4 Veja a saia do comando, veja quie ele esta criando o container:

```bash
# kubectl get pods -o wide
NAME                       READY   STATUS              RESTARTS   AGE   IP          NODE        NOMINATED NODE   READINESS GATES
grafana-5c447577c9-pcpts   1/1     Running             0          47h   10.44.0.5   k8snode01   <none>           <none>
quarto-daemonset-th7pf     0/1     ContainerCreating   0          9s    <none>      k8snode02   <none>           <none>
quarto-daemonset-wttgz     1/1     Running             0          30s   10.44.0.7   k8snode01   <none>           <none>
```

1.5 Aqui ele ja esta criado:

```bash
# kubectl get pods -o wide
NAME                       READY   STATUS    RESTARTS   AGE   IP          NODE        NOMINATED NODE   READINESS GATES
grafana-5c447577c9-pcpts   1/1     Running   0          47h   10.44.0.5   k8snode01   <none>           <none>
quarto-daemonset-th7pf     1/1     Running   0          22s   10.47.0.7   k8snode02   <none>           <none>
quarto-daemonset-wttgz     1/1     Running   0          43s   10.44.0.7   k8snode01   <none>           <none>
```

2.  Vamos agora trabalhar com outro comando muito usado tambemd dentro desse contexto

```bash
# kubectl rollout history daemonset quarto-daemonset 
daemonset.apps/quarto-daemonset 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

2.1 Vamos agora saber mais detalhes da revisao 1:

```bash
# kubectl rollout history daemonset quarto-daemonset --revision=1
daemonset.apps/quarto-daemonset with revision #1
Pod Template:
  Labels:       name=fluentd-elasticsearch
  Containers:
   nginx:
    Image:      nginx:1.7.9
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
```

2.2 Veja que eu tenho duas revisoes ou versoes desse daemonset:

```bash
# kubectl rollout history daemonset quarto-daemonset --revision=2
daemonset.apps/quarto-daemonset with revision #2
Pod Template:
  Labels:       name=fluentd-elasticsearch
  Containers:
   nginx:
    Image:      nginx:1.15.0
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
```

2.3 Agora vamos voltar na versao da revisao 1 que estava anteriormente:

```bash
# kubectl rollout undo daemonset quarto-daemonset --to-revision=1
daemonset.apps/quarto-daemonset rolled back
```

2.4 Precisando verificar que para que possamos trabalhar de forma legal com o atualizao de imagens precisamos de uma feature no codigo.

```yml
updateStrategy:
  type: RollingUpdate
```
