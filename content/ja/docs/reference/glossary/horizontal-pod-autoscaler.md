---
title: ����Pod�����X�P�[���[ (Horizontal Pod Autoscaler)
id: horizontal-pod-autoscaler
date: 2018-04-12
full_link: /docs/tasks/run-application/horizontal-pod-autoscale/
short_description: >
  ����Pod�����X�P�[���[�́A�^�[�Q�b�gCPU�g�p���܂��̓J�X�^�����g���b�N�X�^�[�Q�b�g�Ɋ�Â���Pod���v���J�����X�P�[�����O����API���\�[�X�ł��B

aka: 
- HPA
tags:
- operation
---
����Pod�����X�P�[���[�́A�^�[�Q�b�gCPU�g�p���܂��̓J�X�^�����g���b�N�X�^�[�Q�b�g�Ɋ�Â���{{< glossary_tooltip text="Pod" term_id="pod" >}}���v���J�����X�P�[�����O����API���\�[�X�ł��B

<!--more--> 
HPA�͒ʏ�{{< glossary_tooltip text="ReplicationControllers" term_id="replication-controller" >}}�A{{< glossary_tooltip text="Deployments" term_id="deployment" >}}�܂���{{< glossary_tooltip text="ReplicaSets" term_id="replica-set" >}}�Ŏg�p����܂��BHPA��{{< glossary_tooltip text="DaemonSets" term_id="daemonset" >}}�Ȃǂ̃X�P�[�����O���T�|�[�g���Ȃ��I�u�W�F�N�g�ł͎g�p�ł��܂���B
