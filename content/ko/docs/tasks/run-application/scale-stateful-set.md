---
title: ������ƮǮ��(StatefulSet) ������
content_type: task
weight: 50
---

<!-- overview -->
�� �۾��� ��� {{< glossary_tooltip term_id="StatefulSet"text="������ƮǮ��">}}�� �����ϸ� �ϴ��� �����ش�.
{{< glossary_tooltip term_id="StatefulSet"text="������ƮǮ��">}}�� �����ϸ��ϴ� ���� ���ø�ī ������ �ø��ų� ���̴� ���� �ǹ��Ѵ�.



## {{% heading "prerequisites" %}}


* ������ƮǮ���� �����Ƽ�� ���� 1.5 �̻󿡼��� ����� �� �ִ�.
  �����Ƽ�� ����Ȯ���� ���� ������ Ŀ�ǵ带 ���� `kubectl version`.

* ��� ������ƮǮ ���ø����̼��� ��Ȱ�ϰ� Ȯ����� �ʴ´�. ���� ������ƮǮ���� Ȯ�忡 ���� �� �𸣴� ���, ���� ������ [������ƮǮ�� ����](/docs/concepts/workloads/controllers/statefulset/) �Ǵ� [������ƮǮ�� Ʃ�丮��](/docs/tutorials/stateful-application/basic-stateful-set/) �� �����Ѵ�.

* ������ƮǮ ���ø����̼� Ŭ�����Ͱ� ������ ������ ��쿡�� �����ϸ��� �����ؾ� �Ѵ�.



<!-- steps -->

## ������ƮǮ�� �����ϸ�

### kubectl�� ����Ͽ� ������ƮǮ�� �����ϸ� �ϱ�

���� �����ϸ��� ������ƮǮ���� ã�´�.

```shell
kubectl get statefulsets <stateful-set-name>
```

������ƮǮ���� ���ø�ī ���� �����Ѵ�.

```shell
kubectl scale statefulsets <stateful-set-name> --replicas=<new-replicas>
```

### StatefulSet���� ���÷��̽�(in-place) ������Ʈ �ϱ�

�׷��� ������, ������ƮǮ�¿��� [���÷��̽�(in-place) ������Ʈ(/docs/concepts/cluster-administration/manage-deployment/#in-place-updates-of-resources) �� �� �� �ִ�.

ó���� ������ƮǮ���� `kubectl apply`,�� �ۼ��� ��� ������ƮǮ�� �Ŵ��佺Ʈ�� `.spec.replicas` �� ������Ʈ �� ���� `kubectl apply` �Ѵ�.


```shell
kubectl apply -f <stateful-set-file-updated>
```

�׷��� ������ `kubectl edit`�� ����Ͽ� �ʵ带 �����Ѵ�.


```shell
kubectl edit statefulsets <stateful-set-name>
```

�Ǵ� `kubectl patch` �� ����Ѵ�.

```shell
kubectl patch statefulsets <stateful-set-name> -p '{"spec":{"replicas":<new-replicas>}}'
```

## �����ذ�

### ������ �ٿ��� ���������� �۵����� ����

������ƮǮ���� �����ϴ� �������� pod�� �� ����̶� ������ �ƴ϶�� ������ �ٿ� �Ҽ� ����. 
�����ϴٿ��� ���� �������� pod���� ���� �ǰ� �غ�� �Ŀ� �̷������.


���� spec.replicas > 1 �̸�, �����Ƽ���� ���������� ���� pod�� �Ǵ��� �� ����. �װ��� �������� �����̰ų� �Ͻ����� ���� ������ �� �ִ�. �Ͻ��� ������ ���׷��̵� �Ǵ� ���������� �ʿ��� ��������� ���� �߻��� �� �ִ�.

�������� �������� ���� Pod�� ���������� ���� ���, ������ �������� �ʰ� �����ϸ� �ϸ� ������ƮǮ�� ������� �ùٸ��� �۵��ϴµ� �ʿ��� �ּ� ���ø�ī �� �Ʒ��� ���������� �ִ�. �̷� ���� ������ƮǮ���� ����� �� ���� �� �� �ִ�.

���� �Ͻ����� �������� ���� Pod�� ���������� �ʰ� Pod�� �ٽ� ����� �� �ְ� �Ǵ� ���, �Ͻ����� ������ �����Ͼ� �Ǵ� ������ �ٿ� ������ ������ �� �ִ�.
�Ϻ� �л� �����ͺ��̽��� node�� ���ÿ� join �Ǵ� leave �� �� ������ �߻��Ѵ�.
�̷��� ��� ���ø����̼� ���ؿ��� ��� Ȯ���ϴ� ����� ����ϰ�, ���� ���� ���ø����̼� Ŭ�����Ͱ� ������ ������ ���� �����ϸ��� �����ϴ� ���� ����.



## {{% heading "whatsnext" %}}

[������ƮǮ�� �����ϱ�](/docs/tasks/run-application/delete-stateful-set)�� ���� �� �˾ƺ���.


