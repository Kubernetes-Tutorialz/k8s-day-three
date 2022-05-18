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

