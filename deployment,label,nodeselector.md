## Deployments, label and node selector

1.  Vamos criar o primeiro *deployment.yml* para que possamos testar:

`# kubectl create -f primeiro_deployment.yml`

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: giropops
  name: primeiro-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: UK
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources:
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

1.1 veja que eu adicionei alguns detalhes sobre label no meu *deployment.yml*.
1.2 vamos agora listar os detalhes de cada manifesto que foi criado.

2.  Listando nosso *deployment.yml*

`# kubectl describe deployments.apps -n default primeiro-deployment`

```bash
Name:                   primeiro-deployment
Namespace:              default
CreationTimestamp:      Tue, 08 Mar 2022 19:48:33 -0300
Labels:                 app=giropops
                        run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  dc=UK
           run=nginx
  Containers:
   nginx2:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   primeiro-deployment-57c778d58 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  30m   deployment-controller  Scaled up replica set primeiro-deployment-57c778d58 to 1
```

3.  Listando nosso *replicaet*

`# kubectl describe replicasets.apps -n default primeiro-deployment-57c778d58`

```bash
Name:           primeiro-deployment-57c778d58
Namespace:      default
Selector:       pod-template-hash=57c778d58,run=nginx
Labels:         dc=UK
                pod-template-hash=57c778d58
                run=nginx
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/primeiro-deployment
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  dc=UK
           pod-template-hash=57c778d58
           run=nginx
  Containers:
   nginx2:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  32m   replicaset-controller  Created pod: primeiro-deployment-57c778d58-4s8gg
```

4.  Descrevend o *POD*

`# kubectl describe pods -n default primeiro-deployment-57c778d58-4s8gg`

```bash
Name:         primeiro-deployment-57c778d58-4s8gg
Namespace:    default
Priority:     0
Node:         k8snode02/192.168.0.132
Start Time:   Tue, 08 Mar 2022 06:00:19 -0300
Labels:       dc=UK
              pod-template-hash=57c778d58
              run=nginx
Annotations:  <none>
Status:       Running
IP:           10.47.0.6
IPs:
  IP:           10.47.0.6
Controlled By:  ReplicaSet/primeiro-deployment-57c778d58
Containers:
  nginx2:
    Container ID:   docker://ff0d967e17984e382a263e61e4e92fe82f907cb99c6b3b11a56d3d055d3a420f
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:19da26bd6ef0468ac8ef5c03f01ce1569a4dbfb82d4d7b7ffbd7aed16ad3eb46
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 08 Mar 2022 06:00:24 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-pfcgs (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-pfcgs:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  33m   default-scheduler  Successfully assigned default/primeiro-deployment-57c778d58-4s8gg to k8snode02
  Normal  Pulling    14h   kubelet            Pulling image "nginx"
  Normal  Pulled     14h   kubelet            Successfully pulled image "nginx" in 2.704346073s
  Normal  Created    14h   kubelet            Created container nginx2
  Normal  Started    14h   kubelet            Started container nginx
```

4.1 Trazendo os *PODS* que estao em UK

`# kubectl get pods -n default -l dc=UK`

```bash
NAME                                  READY   STATUS    RESTARTS   AGE
primeiro-deployment-57c778d58-4s8gg   1/1     Running   0          41m
```

4.2 Filtrando por coluna os labels:

`# kubectl get pods -n default -L dc`

```bash
NAME                                  READY   STATUS    RESTARTS   AGE   DC
nginx                                 1/1     Running   0          39h
nginx-85b98978db-ktkxq                1/1     Running   0          39h
nginx-85b98978db-pzhlf                1/1     Running   0          14h
nginx-85b98978db-srpr6                1/1     Running   0          14h
nginx-85b98978db-w49lb                1/1     Running   0          14h
nginx-85b98978db-wt4kg                1/1     Running   0          14h
primeiro-deployment-57c778d58-4s8gg   1/1     Running   0          42m   UK
```

4.3 