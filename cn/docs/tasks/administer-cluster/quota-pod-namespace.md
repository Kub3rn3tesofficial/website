---
title: ���������ռ���pod����
---


{% capture overview %}

������Ҫ�����������һ�������ռ��¿����е�pod��������Դ�����ϸ��Ϣ�ɲ鿴��[��Դ���](/docs/api-reference/v1.7/#resourcequota-v1-core)
��

{% endcapture %}


{% capture prerequisites %}

{% include task-tutorial-prereqs.md %}

{% endcapture %}


{% capture steps %}

## ����һ�������ռ�

���ȴ���һ�������ռ䣬�������Խ����β����д�������Դ�뼯Ⱥ������Դ���뿪����

```shell
kubectl create namespace quota-pod-example
```

## ������Դ���

������һ����Դ���������ļ���

{% include code.html language="yaml" file="quota-pod.yaml" ghlink="/docs/tasks/administer-cluster/quota-pod.yaml" %}

���������Դ��

```shell
kubectl create -f https://k8s.io/docs/tasks/administer-cluster/quota-pod.yaml --namespace=quota-pod-example
```

�鿴��Դ������ϸ��Ϣ��

```shell
kubectl get resourcequota pod-demo --namespace=quota-pod-example --output=yaml
```

���������Ϣ���ǿ��Կ������������ռ���pod�������2����Ŀǰ������pods��Ϊ0�����ʹ����Ϊ0��

```yaml
spec:
  hard:
    pods: "2"
status:
  hard:
    pods: "2"
  used:
    pods: "0"
```

������һ��Deployment�������ļ���

{% include code.html language="yaml" file="quota-pod-deployment.yaml" ghlink="/docs/tasks/administer-cluster/quota-pod-deployment.yaml" %}

�������ļ��У� `replicas: 3` ����kubernetes���Դ�������pods����������ͬ��Ӧ�á�

�������Deployment��

```shell
kubectl create -f https://k8s.io/docs/tasks/administer-cluster/quota-pod-deployment.yaml --namespace=quota-pod-example
```

�鿴Deployment����ϸ��Ϣ��

```shell
kubectl get deployment pod-quota-demo --namespace=quota-pod-example --output=yaml
```

���������Ϣ���ǿ��Կ��������ܳ��Դ�������pod�����������������ƣ�ֻ������pod�ܱ��ɹ�������

```yaml
spec:
  ...
  replicas: 3
...
status:
  availableReplicas: 2
...
lastUpdateTime: 2017-07-07T20:57:05Z
    message: 'unable to create pods: pods "pod-quota-demo-1650323038-" is forbidden:
      exceeded quota: pod-demo, requested: pods=1, used: pods=2, limited: pods=2'
```

## ����

ɾ�������ռ䣺

```shell
kubectl delete namespace quota-pod-example
```

{% endcapture %}

{% capture whatsnext %}

### ���ڼ�Ⱥ����

* [���������ռ��£��ڴ�Ĭ�ϵ�requestֵ��limitֵ](/docs/tasks/administer-cluster/memory-default-namespace/)

* [���������ռ��£�CPUĬ�ϵ�requestֵ��limitֵ](/docs/tasks/administer-cluster/cpu-default-namespace/)

* [���������ռ��£��ڴ����Сֵ�����ֵ](/docs/tasks/administer-cluster/memory-constraint-namespace/)

* [���������ռ��£�CPU����Сֵ�����ֵ](/docs/tasks/administer-cluster/cpu-constraint-namespace/)

* [���������ռ��£��ڴ��CPU�����](/docs/tasks/administer-cluster/quota-memory-cpu-namespace/)

* [���������ռ��£�API��������](/docs/tasks/administer-cluster/quota-api-object/)

### ����Ӧ�ÿ���

* [��������pod�����ڴ���Դ](/docs/tasks/configure-pod-container/assign-memory-resource/)

* [��������pod����CPU��Դ](/docs/tasks/configure-pod-container/assign-cpu-resource/)

* [����pod��QoS](/docs/tasks/configure-pod-container/quality-service-pod/)

{% endcapture %}


{% include templates/task.md %}


