<hh1>Gerenciamento de Distribuição de Pods</h1>

<h2>Node Selector</h2>

1. No manifesto yaml será configurado o volume e inserido uma label um um dos nós;
2. Na epecificação do MongoDb será utilzado o Host Path;
3. Com o Node Selector é possivel especificar no manifesto do Pod MongoDb qual NODE ele deve
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

<h3><Testes/h3>

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
Depois exclua o pod do MongoDb.
E faça uma conuslta, os dados irão retornar devido ao Node Selector.

3. Terceiro teste

Retirar a label no node:
``` bash
$ kubectl label node pool-uzfx4xaht-c70hh database-
``` 

Excluir o pod:
```
$ kubectl get pods nomeDoPod
```

Consulte o pod, observe que ele ficará com status de "pending".


4. Teste

Retirar a label no node:
``` bash
$ kubectl label node pool-uzfx4xaht-c70hh database-
``` 

Excluir e consultar o pod diversas vezes:
```
$ kubectl get pods nomeDoPod
$ kubectl get pods -o wide
```

Aleatoriamente o pod ficará alternando entre os Nodes.

Quando o POD estiver vinculado a um NODE diferente do utilizado par configurar a LABEL,
faça uma consulta a base de dados.

Ao usar o Host Path caso o pod sejá excluido não é garantido que o novo pod
será direcionado para mesmo NODE, então não é uma boa abordagem sem a utilização
do Node Selector.



