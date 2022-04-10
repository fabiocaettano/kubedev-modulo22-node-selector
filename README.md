<hh1>Gerenciamento de Distribuição de Pods</h1>

<h2>Node Selector</h2>

1. No manifesto yaml será configurado o volume e inserido uma label em um dos nós;
2. Na epecificação do MongoDb será utilzado o Host Path;
3. Com o Node Selector é possivel especificar no manifesto do Pod qual NODE ele deve
ser vinuclado. 

<h3>Configurar o Manifesto Deployment do MongoDb</h3>

1. Na especificação do container mongodb foi incluido o volumeMounts.
``` yaml
volumeMounts:
    - mountPath: /data/db
    name: mongo-data
```

2. Na especificação do MongoDb foi incluido o volume do tipo Host Path:
``` yaml
volumes:
    - name: mongo-data
      hostPath:          
        path: /volumes/mongodb
```

3. Na especificação do MongoDb foi incuido o Node Selector:
``` yaml
nodeSelector:
    database: mongodb
```

<h3>Aplicar o Manifesto</h3>

1. Consultar e escolher um os nodes:
``` bash
$ kubectl get nodes
```

2. No comando abaixo informe o node escolhido:
``` bash
$ kubectl label node pool-uzfx4xaht-c70hh database=mongodb
```

3. Visualizar a configuração do Node, onde poderá ser localizado o label configurado:
``` bash
$ kubectl describe node kubectl pool-uzfx4xaht-c70hh | less
```

4. Aplicar a configuração:
``` bash
$ kubectl appply -f ./mongodb
```

<h3>Testes</h3>

1. Primeiro Teste

Observe qual NODE o pod do MongoDb está vinculado.
``` bash
$ kubectl get pods -o wide
```

Excluir o pod e consulte novamente, e observe que o pod do mongodb sempre será 
vinculado ao node com a label.
```
$ kubectl delete pod nomeDoPod
$ kubectl get pods -o wide
```

2. Segundo Teste

Incuir dados na base de dados.

Exemplo:

![image](https://github.com/fabiocaettano/kubedev-modulo22-node-selector/blob/main/image/post.PNG)

Consultar e anotar o nome do pod do MongoDb:

``` bash
$ kubectl get pods
```

Excluir o pod do MongoDb:
``` bash
$ kubectl delete pod nome
```

Faça uma conuslta, os dados irão retornar devido ao Node Selector.

![image](https://github.com/fabiocaettano/kubedev-modulo22-node-selector/blob/main/image/get.PNG)



3. Terceiro teste

Retirar a label no node:
``` bash
$ kubectl label node pool-uzfx4xaht-c70hh database-
``` 

Excluir o pod do MongoDb:
``` bash
$ kubectl delete pod nome
```

Consulte o pod, observe que ele ficará com status de "pending".

![image](https://github.com/fabiocaettano/kubedev-modulo22-node-selector/blob/main/image/get.PNG)


4. Teste

Com a retirara a label no node , excluia e consulte o pod diversas vezes:
```
$ kubectl get pods nomeDoPod - o wide
$ kubectl delete pod nomeDoPod
```

Aleatoriamente o pod ficará alternando entre os Nodes.
