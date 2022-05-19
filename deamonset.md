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

