---
title: ͨ������������Pod��Ϣ���ָ�����
---

{% capture overview %}

��ҳ����ʾ��Pod���ʹ�û����������Լ�����Ϣ���ָ�pod�����е������������������Գ���pod���ֶκ������ֶΡ�

�����ַ�ʽ���Խ�Pod��Container�ֶγ��ָ������е�������
�������� ��[DownwardAPIVolumeFiles](/docs/resources-reference/{{page.version}}/#downwardapivolumefile-v1-core).
�����ֳ���Pod��Container�ֶεķ�ʽ����Ϊ*Downward API*��

{% endcapture %}


{% capture prerequisites %}

{% include task-tutorial-prereqs.md %}

{% endcapture %}


{% capture steps %}

## Downward API

�����ַ�ʽ���Խ�Pod��Container�ֶγ��ָ������е�������

* ��������
* [DownwardAPIVolumeFiles](/docs/resources-reference/{{page.version}}/#downwardapivolumefile-v1-core)

�����ֳ���Pod��Container�ֶεķ�ʽ����Ϊ*Downward API*��


## ��Pod�ֶ���Ϊ����������ֵ

�������ϰ�У��㽫����һ������һ��������pod�����Ǹ�pod�������ļ���

{% include code.html language="yaml" file="dapi-envars-pod.yaml" ghlink="/cn/docs/tasks/inject-data-application/dapi-envars-pod.yaml" %}

��������ļ��У�����Կ����������������`env`�ֶ���һ��[EnvVars](/docs/resources-reference/{{page.version}}/#envvar-v1-core)���͵����顣
�����е�һ��Ԫ��ָ��`MY_NODE_NAME`�������������Pod��`spec.nodeName`�ֶλ�ȡ����ֵ��ͬ����������������Ҳ�Ǵ�Pod���ֶλ�ȡ���ǵı���ֵ��

**ע��:** ��ʾ���е��ֶ���Pod�ֶΣ�����Pod���������ֶΡ�
{: .note}

����Pod��

```shell
kubectl create -f https://k8s.io/cn/docs/tasks/inject-data-application/dapi-envars-pod.yaml
```

��֤Pod�е���������������

```
kubectl get pods
```

�鿴������־��

```
kubectl logs dapi-envars-fieldref
```

�����Ϣ��ʾ����ѡ��Ļ���������ֵ��

```
minikube
dapi-envars-fieldref
default
172.17.0.4
default
```

Ҫ�˽�Ϊʲô��Щֵ����־�У���鿴�����ļ��е�`command` �� `args`�ֶΡ� ����������ʱ�������������������ֵд��stdout��ÿʮ���ظ�ִ��һ�Ρ�

������������Pod�����е���������һ��shell��

```
kubectl exec -it dapi-envars-fieldref -- sh
```

��shell�У��鿴����������

```
/# printenv
```

�����Ϣ��ʾ���������Ѿ�ָ��ΪPod���ֶε�ֵ��

```
MY_POD_SERVICE_ACCOUNT=default
...
MY_POD_NAMESPACE=default
MY_POD_IP=172.17.0.4
...
MY_NODE_NAME=minikube
...
MY_POD_NAME=dapi-envars-fieldref
```

## �������ֶ���Ϊ����������ֵ

ǰ�����ϰ�У��㽫Pod�ֶ���Ϊ����������ֵ�������������ϰ���㽫�������ֶ���Ϊ����������ֵ�������ǰ���һ��������pod�������ļ���

{% include code.html language="yaml" file="dapi-envars-container.yaml" ghlink="/cn/docs/tasks/inject-data-application/dapi-envars-container.yaml" %}

��������ļ��У�����Կ����ĸ�����������`env`�ֶ���һ��[EnvVars](/docs/resources-reference/{{page.version}}/#envvar-v1-core)
���͵����顣�����е�һ��Ԫ��ָ��`MY_CPU_REQUEST`�������������������`requests.cpu`�ֶλ�ȡ����ֵ��ͬ����������������Ҳ�Ǵ��������ֶλ�ȡ���ǵı���ֵ��

����Pod��

```shell
kubectl create -f https://k8s.io/cn/docs/tasks/inject-data-application/dapi-envars-container.yaml
```

��֤Pod�е���������������

```
kubectl get pods
```

�鿴������־��

```
kubectl logs dapi-envars-resourcefieldref
```

�����Ϣ��ʾ����ѡ��Ļ���������ֵ��

```
1
1
33554432
67108864
```

{% endcapture %}

{% capture whatsnext %}

* [Defining Environment Variables for a Container](/docs/tasks/configure-pod-container/define-environment-variable-container/)
* [PodSpec](/docs/resources-reference/{{page.version}}/#podspec-v1-core)
* [Container](/docs/resources-reference/{{page.version}}/#container-v1-core)
* [EnvVar](/docs/resources-reference/{{page.version}}/#envvar-v1-core)
* [EnvVarSource](/docs/resources-reference/{{page.version}}/#envvarsource-v1-core)
* [ObjectFieldSelector](/docs/resources-reference/{{page.version}}/#objectfieldselector-v1-core)
* [ResourceFieldSelector](/docs/resources-reference/{{page.version}}/#resourcefieldselector-v1-core)

{% endcapture %}


{% include templates/task.md %}
