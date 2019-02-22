---
���⣺'Kubernetes 1.10���ȶ��洢����ȫ������'
���ߣ�kbarnard
��ǩ��
���ڣ�2018-03-26
modified_time��'2018-03-27T11��01��39.569-07��00'
blogger_id��tag��blogger.com��1999��blog-112706738355446097.post-6519705795358457586
blogger_orig_url��https��//kubernetes.io/blog/2018/03/kubernetes-1.10-stabilizing-storage-security-networking
slug��kubernetes-1.10-stabilizing-storage-security-networking
���ڣ�2018-03-26
---

***����ע�������������[1.10����
�Ŷ�]��https://github.com/kubernetes/sig-release/blob/master/releases/release-1.10/release_team.md��***

���Ǻܸ��˵������Ƴ�Kubernetes 1.10���������ǵĵ�һ���汾
2018�꣡

����ķ��������ƽ�����ȣ�����չ�ԺͿɲ����
Kubernetes��������°汾�ȶ���3���ؼ�����Ĺ��ܣ�
�����洢����ȫ�����硣�˰汾��ֵ��ע��Ĳ���
���������ⲿkubectlƾ֤�ṩ����alpha����
�ܹ��ڰ�װʱ��beta����DNS�����л���CoreDNS���Լ��ƶ�
�����洢�ӿڣ�CSI���ͳ־ñ��ؾ�beta��

�����������˽�˰汾����Ҫ���ܣ�

##�洢 -  CSI�ͱ��ش洢ת����԰�

���[�洢������ȤС��]��˵��һ����Ӱ�����İ汾
��SIG��]��https://github.com/kubernetes/community/tree/master/sig-storage����
��־�������ڶ��ֹ��ܷ���Ĺ����ﵽ���塣 [Kubernetes
ʵʩ]��https://github.com/kubernetes/features/issues/178��
[��װ��洢
�ӿڣ�https://github.com/container-storage-interface/spec/blob/master/spec.md��
��CSI���ڴ˰汾��ת����԰棺���ڰ�װ�µľ���
���ɲ���pod���ⷴ������ʹ�������洢�ṩ���ܹ�
�ں���Kubernetes�����֮������������ǵĽ��������
�������Kubernetes��̬ϵͳ�еĿ���չ�ԡ�

[���ã��ǹ������ش洢
����]��https://github.com/kubernetes/features/issues/121����չ��
�˰汾�еĲ��԰棬ʹ�������ӣ����������ӣ��洢
�������־þ�Դ������ζ�Ÿ��ߵ����ܺ�
���ͷֲ�ʽ�ļ�ϵͳ�����ݿ�ĳɱ���

�˰汾��������Persistent Volumes�������¡� Kubernetes����
�Զ�[��ֹɾ������ʹ�õĳ־��Ծ�����
һ������]��https://github.com/kubernetes/features/issues/498�������԰棩��[Ԥ��
ɾ���󶨵��־þ������ĳ־þ�
]��https://github.com/kubernetes/features/issues/499�������԰棩����������ȷ��
����ȷ��˳��ɾ���洢API����

##��ȫ�� - �ⲿƾ���ṩ����alpha��

Kubernetes����
�Ѿ��߶ȿ���չ����1.10�л������һ����չ��
[�ⲿkubectlƾ֤
�ṩ��]��https://github.com/kubernetes/features/issues/541����alpha������
�ṩ�̣���Ӧ�̺�����ƽ̨���������ڿ��Է����������ļ�
���ڴ����ض����ṩ��IAM����������֤�Ĳ������
�벻��֧�ֵ��ڲ������֤ϵͳ����
in-tree������Active Directory���ⲹ����[�ƿ�����
������]��https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/��
������1.9����ӡ�

## Networking  -  CoreDNS��ΪDNS�ṩ�̣����԰棩

�ܹ�[�л�DNS
����]��https://github.com/kubernetes/website/pull/7638����CoreDNS
[��װʱ��]��https://kubernetes.io/docs/tasks/administer-cluster/coredns/��
Ŀǰ���ڲ��Խ׶Ρ� CoreDNS���и��ٵ��ƶ�����������һ����ִ���ļ���һ��
�������̣���֧������������

�����ڵ�ÿ��������ȤС�飨SIG�����ڼ����ṩ
����Ҫ��������ǿ���ܣ��޸�����͹���
רҵ�����й�SIG�����������б������
[����
����]��https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#110-release-notes����

���� ������

Kubernetes 1.10�ɹ�[����
GitHub��]��https://github.com/kubernetes/kubernetes/releases/tag/v1.10.0����Ҫ�õ�
��Kubernetes��ʼ��������Щ��[nteractive
�̳�]��https://kubernetes.io/docs/tutorials/����

## 2����ɫ����ϵ��

���������Ȥ̽����Щ����
�����룬��ع��������ǵ�2��Kubernetesϵ��
���ǽ��ص�������¹��ܵ���ϸ������

��1�� -  Kubernetes��Beta�����洢�ӿڣ�CSI��
��2�� -  Kubernetes�ı��س���������Beta

##�����Ŷ�

����汾��ͨ�����ٸ���Ŭ��ʵ�ֵ�
���׼����ͷǼ������ݵĸ��ˡ� �ر�
��л[����
�Ŷӣ�https://github.com/kubernetes/sig-release/blob/master/releases/release-1.10/release_team.md��
��Kubernetes΢���ʹJaice Singer DuMars�쵼�� 10
�����Ŷ��еĸ���Э����������෽�棬����
�ĵ������ԣ���֤�͹��������ԡ�

����Kubernetes�����ķ�չ�����ǵķ������̴�����һ��
�ڿ�Դ��������н��к����ľ�����ʾ��
Kubernetes����Ѹ�ٻ�����û�����������������һ��
�����ķ���ѭ��������Ĺ������ύ���봴������
����������

## Project Velocity

CNCF��������һ�����Ĳ�������Ŀ
���ӻ�������Ŀ���������ס� [K8S
DevStats]��https://devstats.k8s.io/��˵���˹��׵�ϸ��
������Ҫ��˾�����ߣ��Լ�һ������ӡ����̵�Ԥ����
����Ӹ��˹����ߵ���ȡ�����������ڵ���������
���������Զ����̶���ߣ�����ڷ�������ʱ���������
���Ը��ڿ�ʼʱ��ˮƽ�����־��һ��רҵ
ת������ɹ����ԡ� Kubernetes������75,000��������
GitHub��������ҵ���Ŀ֮һ��

##�û�����

����[�����CNCF
����]��https://www.cncf.io/blog/2018/03/26/cncf-survey-china/��������49��
�����ܷ���ʹ��Kubernetes��������������49��
�����������������ѽ�����ȫ������֯
ʹ��[Kubernetes in production]��https://kubernetes.io/case-studies/��at
��ģ�Ӵ���������������û����°�����
1. **��Ϊ**�����й����ĵ����豸������
���磬[�ƶ����ڲ�IT���ŵ�Ӧ�ó����������
Kubernetes]��https://kubernetes.io/case-studies/huawei/�����⵼����
ȫ�������ڴ�һ�ܼ��ٵ������ӣ��Լ�Ч��
Ӧ�ó��򽻸������ʮ����
1. **����֮�ù���**�����OTA�;Ƶ�֮һ
��˾��ʹ��Kubernetes [�������ǵ��������
�ٶȣ�https://www.linux.com/blog/managing-production-systems-kubernetes-chinese-enterprises��
�Ӽ�Сʱ�������ӡ����⣬��������Kubernetes����
�����߹������صĿ���չ�ԺͿ����ԡ�
1. **ʹ�õ¹�ý��������˾Haufe Group **
Kubernetes [�԰����ڷ����°汾
Сʱ]��https://kubernetes.io/case-studies/haufegroup/�������Ǽ��졣��
��˾Ҳ������ҹ����������Լһ���������
��ʡ30����Ӳ���ɱ���
1. **�����������ʲ�����˾������**�ܹ�Ѹ�ٲ�ȡ�ж�
ʹ��Kubernetes������һ��Ͷ�����о�����Ӧ�ó����[��ʼ��
��100�����ڽ���]��https://kubernetes.io/case-studies/blackrock/����
Kubernetes�ܰ�������Ŷ��� [�������
����]��https://docs.google.com/a/google.com/forms/d/e/1FAIpQLScuI7Ye3VQHQTwBASrgkjQDSS5TP0g3AXfFhwSM9YpHgxRKFA/viewform��
��������

##��̬ϵͳ����

1. CNCF���ڽ�����֤��Ʒ��չ��
������֤��Kubernetes Application Developer���ԡ� CKAD����
֤��������ƣ����������ú͹���������
Kubernetes����ԭ��Ӧ�ó��� CNCF����Ѱ��beta������Ա
����½�Ŀ�������ҵ�������Ϣ
[����]��https://www.cncf.io/blog/2018/03/16/cncf-announces-ckad-exam/����
1. Kubernetes�ĵ����ھ���[�û�
�ó�]��https://k8s.io/docs/home/��������ѧϰ���ض�;��
������˭����������ʲô��ѧϰKubernetes������
���ڳ�ѧ����˵���������κ�ʱ�򶼸��о��飬�û������ҵ������ó�
�ض��ڼ�Ⱥ����Ա��Ӧ�ó��򿪷���Ա��
1. CNCF���ṩ[����
ѵ��]��https://www.cncf.io/certification/training/�����ڼ���
��Ҫ������������ʵ�����Kubernetes��Ⱥ��

## KubeCon

����������Kubernetes�ۻᣬ[KubeCon +
CloudNativeCon]��https://events.linuxfoundation.org/events/kubecon-cloudnativecon-europe-2018/��
����2018��5��2����4��ǰ���籾�����������Լ���Ϊ��ɫ
�γ̣������о������������Ǳˮ��ɳ���ȵȣ�����
[�ճ�]��https://events.linuxfoundation.org/events/kubecon-cloudnativecon-europe-2018/program/schedule/��
�����ߺ�
[ע��]��https://events.linuxfoundation.org/events/kubecon-cloudnativecon-europe-2018/attend/register/��
���죡

##�������ֻ�

4��10�ռ���Kubernetes 1.10�����Ŷӵĳ�Ա
����10��PDT�˽�˰汾����Ҫ���ܣ�����Local
�־þ�������洢�ӿڣ�CSI�����Ĵ���
[����]��https://www.cncf.io/event/webinar-kubernetes-1-10/����

���� ��������

����Kubernetes��򵥵ķ������Ǽ���
�ڶ�[�ر���Ȥ֮һ]
����]��https://github.com/kubernetes/community/blob/master/sig-list.md��
��SIGs������������Ȥ�� �����벥�ŵĶ���
��Kubernetes������ �����ǵ�ÿ��[����]������������
����]��https://github.com/kubernetes/community/blob/master/communication.md#weekly-meeting����
��ͨ������������

��л���ĳ���������֧�֡�
1.��[Stack]�Ϸ������⣨��ش����⣩
�����http://stackoverflow.com/questions/tagged/kubernetes��
1.����[K8sPort]��http://k8sport.org/���ĳ����������Ż���վ
1.��Twitter�Ϲ�ע����[@Kubernetesio]��https://twitter.com/kubernetesio��
���¸���
1.��[Slack]�����������죨http://slack.k8s.io/��
1.�������Kubernetes
[����]��https://docs.google.com/a/linuxfoundation.org/forms/d/e/1FAIpQLScuI7Ye3VQHQTwBASrgkjQDSS5TP0g3AXfFhwSM9YpHgxRKFA/viewform����