
<!--
Joins a machine as a control plane instance
-->
加入计算机作为控制平面实例

<!--
### Synopsis
-->
### 概要


<!--
Joins a machine as a control plane instance
-->
加入计算机作为控制平面实例

```
kubeadm join phase control-plane-join all [flags]
```

<!--
### Options
-->
### 选项

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--apiserver-advertise-address string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      If the node should host a new control plane instance, the IP address the API Server will advertise it's listening on. If not set the default network interface will be used.
      -->
      如果该节点应托管一个新的控制平面实例，此值则为 API 服务器将通告的其所侦听的 IP 地址。如果未设置，将使用默认网络接口。
      </td>
    </tr>
    
    <tr>
      <td colspan="2">--config string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      Path to kubeadm config file.
      -->
      kubeadm 配置文件的路径。
      </td>
    </tr>
    
    <tr>
      <td colspan="2">--experimental-control-plane</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      Create a new control plane instance on this node
      -->
      在此节点上创建一个新的控制平面实例
      </td>
    </tr>
    
    <tr>
      <td colspan="2">-h, --help</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      help for all
      -->
      all 操作的帮助信息      </td>
    </tr>
    
    <tr>
      <td colspan="2">--node-name string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      Specify the node name.
      -->
      指定节点名称。
      </td>
    </tr>

  </tbody>
</table>



<!--
### Options inherited from parent commands
-->
### 从父命令继承的选项

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--rootfs string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">
      <!--
      [EXPERIMENTAL] The path to the 'real' host root filesystem.
      -->
      [实验性]“真实”主机根文件系统的路径。
      </td>
    </tr>

  </tbody>
</table>





