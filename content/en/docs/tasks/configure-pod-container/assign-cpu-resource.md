---
title: Especificar Recursos de CPU aos Contêiners e Pods
content_type: task
weight: 20
---

<!-- overview -->

Esta página mostra como atribuir um *requisito* de CPU e um *limite* de CPU a um contêiner. Contêineres não podem usar mais CPU do que o limite configurado. Comprovado que o sistema dispõe de tempo de CPU livre, o contêiner pode alocar tanta CPU quanto necessária para suas requisições.

## {{% heading "prerequisites" %}}

{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}

Seu cluster precisa ter ao menos 1 CPU disponível para uso, a fim de executar as tarefas de exemplos.
Your cluster must have at least 1 CPU available for use to run the task examples.

Alguns dos passos nesta página requerem que você execute o serviço [metric-server](https://github.com/kubernetes-sigs/metrics-server) no seu cluster. Se você já tiver o metrics-server executando, você pode pular estes passos.

Se você estiver executando {{< glossary_tooltip term_id="minikube" >}}, execute o comando a seguir para habilitar o metrics-server:

```shell
minikube addons enable metrics-server
```

```shell
minikube addons enable metrics-server
```

Para ver se o metrics-server (ou outro fornecedor de API de métricas de recursos, `metrics.k8s.io`) está executando, digite o comando seguinte:

```shell
kubectl get apiservices
```

Se a API de métricas de recursos estiver disponível, a saída incluirá a referência à `metrics.k8s.io`.


```
NAME
v1beta1.metrics.k8s.io
```


<!-- steps -->

## Create a namespace

Crie um {{< glossary_tooltip term_id="namespace" >}} assim os seus recursos criados neste exercício estarão isolados do resto do seu cluster.

```shell
kubectl create namespace cpu-example
```

## Specify a CPU request and a CPU limit

Para especificar os requisitos de CPU para um contêiner, inclua o campo `resources:requests` no manifersto de recursos do Contêiner. Para especificar o limite de CPU, inclua `resources:limits`.

Neste exercício, você cria um Pod que têm um contêiner. O contêiner têm um requisito de 0,5 CPU e o limite de 1 CPU. Aqui está o arquivo de configuração do Pod:

{{< codenew file="pods/resource/cpu-request-limit.yaml" >}}

A seção `args` do arquivo de configuração fornece parâmetros para o contêiner quando ele inicia.
O parâmetro `-cpus "2"` diz ao Contêiner para tentar usar 2 CPUs.


Criar o Pod:

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/cpu-request-limit.yaml --namespace=cpu-example
```

Verificar se o Pod está executando:

```shell
kubectl get pod cpu-demo --namespace=cpu-example
```

Ver informação detalhada sobre o Pod:

```shell
kubectl get pod cpu-demo --output=yaml --namespace=cpu-example
```

A saída mostra que este contêiner no Pod tem um requisito de CPU de 500 milliCPU e um limite de CPU de 1 CPU.

```yaml
resources:
  limits:
    cpu: "1"
  requests:
    cpu: 500m
```
Use `kubectl top` para buscar as métricas do pod:


```shell
kubectl top pod cpu-demo --namespace=cpu-example
```

Este exemplo de saída mostra que o Pod está usando 974 milliCPU, que é ligeiramente menor que o limite de 1 CPU especificado na configuração do Pod.

```
NAME                        CPU(cores)   MEMORY(bytes)
cpu-demo                    974m         <something>
```

Lembre-se que ao especificar `-cpu "2"`, você configurou o Contêiner para tentar usar 2 CPUs, mas o Contêiner está apenas habilitado a usar cerca de 1 CPU. O uso de CPU dos contêneres está sendo estrangulado, porque o contêiner está tentando usar mais recursos de CPU que o seu limite. 

{{< note >}}
Outra possível explicação para o uso de CPU estar abaixo de 1,0 é que o Nó não dispõe de recuros de CPU suficientes disponíveis. Lembre-se que o pré-requisito para este exercício é que o seu cluster tenha disponível ao menos 1 CPU para uso. Se o seu Contêiner executa em um Nó que tem apenas 1 CPU, o Contêiner não pode usar mais que 1 CPU, sem contar o limite de CPU especificado para o Contêiner. 

{{< /note >}}

## CPU units

Os recursos de CPU são medidos em unidades de *CPU*. Uma CPU, em Kunernetes, é equivalente a:

* 1 AWS vCPU
* 1 GCP Core
* 1 Azure vCore
* 1 Hyperthread em um processador Intel bare-metal com Hyperthreading


Valoes fracionários são aceitos. Ao Contêiner que requer 0,5 CPU é garantido pelo menos a metade da CPU que um Contêiner que requer 1 CPU. Você pode usar o sufixo m para representar milli. Por exemplo 100m CPU, 100 miliiCPU, e 0,1 CPU são todas a mesma. Precisão menor que 1m não é aceita.

CPU é sempre requisitada como uma quantidade absoluta, nunca como uma quantidade relativa; 0,1 é a mesma quantidadede de CPU em uma máquina single-core, dual-core, ou 48-core.

Apagando seu Pod:

```shell
kubectl delete pod cpu-demo --namespace=cpu-example
```

## Specify a CPU request that is too big for your Nodes

Requisitos de CPU e limites são associados aos Contêineres, mas é funcional pensar num Pod como tendo requisito e limite de CPU. O requisito de CPU para um Pod é a soma dos requisitos de CPU de todos os Contêineres no Pod. Da mesma forma, o limite de CPU para um Pod é a soma dos limites de CPU para todos os Contêineres no Pod. 

O agendamento do Pod é baseado em requisições. Um Pod é agendado para executar em um Nó somente se o Nó possuir suficiente recursos de CPU disponíveis para satisfazer os requisitos de CPU do Pod.

Neste exercício, você cria um Pod que tem um requisito de CPU tão grande que excede a capacidade de qualquer Nó em seu cluster. Aqui está o arquivo de configuração para o Pod que tem um Contêiner. O Contêiner requer 100 CPU, que provávelmente excede a capacidade de qualquer Nó em seu cluster.

{{< codenew file="pods/resource/cpu-request-limit-2.yaml" >}}


Criando o Por:

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/cpu-request-limit-2.yaml --namespace=cpu-example
```

Vendo o status do Pod:


```shell
kubectl get pod cpu-demo-2 --namespace=cpu-example
```

A saída mostra que o status do Pod é Pending. Que significa, que o Pod não está agendado para executar em qualquer Nó, e permanecerá no estado Pending indefinidamente.


```
NAME         READY     STATUS    RESTARTS   AGE
cpu-demo-2   0/1       Pending   0          7m
```

Ver informação detalhada sobre o Pod, incluindo eventos:


```shell
kubectl describe pod cpu-demo-2 --namespace=cpu-example
```

A saída mostra que o Contêiner não pode ser agendado devido a insuficiência de recursos de CPU nos Nós: 


```
Events:
  Reason                        Message
  ------                        -------
  FailedScheduling      No nodes are available that match all of the following predicates:: Insufficient cpu (3).
```

Apagar seu Pod:

```shell
kubectl delete pod cpu-demo-2 --namespace=cpu-example
```

## If you do not specify a CPU limit

Se você não especificar o limite de CPU para um Contêiner, então uma destas situações se aplica:

* O Contêiner não possue limite superior de recursos de CPU que possa usar. O Contêiner poderia usar todos os recursos de CPU disponíveis no Nó em que estiver executando. 


O Contêiner está executamdo em um namespace que tem um limite de CPU padrão, e ao Contêiner é atribuído o limite padrão automaticamente. Administradores de cluster podem usar [LimitRange](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#limitrange-v1-core/) para especificar o valor padrão do limite de CPU.


## If you specify a CPU limit but do not specify a CPU request


Se você especificar o limite de CPU para um Contêiner mas não especificar os requisitos de CPU, o Kubernetes automaticamente irá atribuir os requisitos de CPU que correspondem ao limite. Similarmente, se o Contêiner especifica seu próprio limite de memória mas não especifica os requisitos de memória, o kubernetes automáticamente atribui os requisitos de memória que correspondem ao limite.

## Motivation for CPU requests and limits

Ao configurar os requisitos de CPU e limites dos Contêneres que executam no seu cluster, você faz um uso eficiente dos recursos de CPU disponívies nos Nós do seu cluster. Ao pegar baixos recursos de CPU de um Pod, você proporciona ao Pod uma boa chance de ser agendado. Ao pegar um limite de CPU que é maior que os requisitos de CPU, você estará realizando duas coisas: 

* o Pod pode ter estouros de atividade quando estiver fazendo uso de recursos de CPU que deveriam estar disponíveis.
* O total de recursos de CPU que um Pod pode usar durante um estouro é limitado a uma quantidade razoável.

## Clean up

Apagando seu namespace:

```shell
kubectl delete namespace cpu-example
```



## {{% heading "whatsnext" %}}



### For app developers

* [Assign Memory Resources to Containers and Pods](/docs/tasks/configure-pod-container/assign-memory-resource/)

* [Configure Quality of Service for Pods](/docs/tasks/configure-pod-container/quality-service-pod/)

### For cluster administrators

* [Configure Default Memory Requests and Limits for a Namespace](/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)

* [Configure Default CPU Requests and Limits for a Namespace](/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)

* [Configure Minimum and Maximum Memory Constraints for a Namespace](/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/)

* [Configure Minimum and Maximum CPU Constraints for a Namespace](/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/)

* [Configure Memory and CPU Quotas for a Namespace](/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)

* [Configure a Pod Quota for a Namespace](/docs/tasks/administer-cluster/manage-resources/quota-pod-namespace/)

* [Configure Quotas for API Objects](/docs/tasks/administer-cluster/quota-api-object/)


