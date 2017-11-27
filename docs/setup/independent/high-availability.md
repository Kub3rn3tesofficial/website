# High Availability

## Set up etcd

1. Create 3 Virtual Machines that follow [CoreOS' hardware recommendations](https://coreos.com/etcd/docs/latest/op-guide/hardware.html).
   For the sake of simplicity, we will refer to them as `etcd0`, `etcd1` and `etcd2`.

### create CA

1. SSH into `etcd0` and run the following:

```
mkdir ~/certs
cd ~/certs
```
```
cat >ca-config.json <<EOL
{
    "signing": {
        "default": {
            "expiry": "43800h"
        },
        "profiles": {
            "server": {
                "expiry": "43800h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth",
                    "client auth"
                ]
            },
            "client": {
                "expiry": "43800h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "client auth"
                ]
            },
            "peer": {
                "expiry": "43800h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth",
                    "client auth"
                ]
            }
        }
    }
}
EOL
```
```
cat >ca-csr.json <<EOL
{
    "CN": "etcd",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "US",
            "L": "CA",
            "O": "My Company Name",
            "ST": "San Francisco",
            "OU": "Org Unit 1",
            "OU": "Org Unit 2"
        }
    ]
}
EOL
```

1. Ensure that the names section in `ca-csr.json` matches your own, or that you use a suitable default.
1. Next, generate the CA certs like so:

```
cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
```

### generate client certs

1. Next you will generate the client certificates. Whilst on `etcd0`, run the following:

```
cat >client.json <<EOL
{
    "CN": "client",
    "key": {
        "algo": "ecdsa",
        "size": 256
    },
    "names": [
        {
            "C": "US",
            "L": "CA",
            "ST": "San Francisco"
        }
    ]
}
EOL
```
```
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=client client.json | cfssljson -bare client
```

This should result in `client.pem` and `client-key.pem` being created.

### set environment vars

1. Open new new tabs in your shell for `etcd1` and `etcd2`. Ensure you are SSHed into all three VMs are run the following (it will be a lot quicker if you use tmux syncing - to do this in iTerm enter `cmd+shift+i`):

```
export PEER_NAME=$(hostname)
export PUBLIC_IP=$(ip addr show eth0 | grep -Po 'inet \K[\d.]+')
export PRIVATE_IP=$(ip addr show eth1 | grep -Po 'inet \K[\d.]+')

touch /etcd/etcd.env
echo "PEER_NAME=$PEER_NAME" >> /etcd/etcd.env
echo "PUBLIC_IP=$PUBLIC_IP" >> /etcd/etcd.env
echo "PRIVATE_IP=$PRIVATE_IP" >> /etcd/etcd.env
```

### create ssh access

1. Your `etcd1` and `etcd2` VMs need access to the CA certs created by `etcd0`. To do this, we'll use SCP which relies on SSH. So let's generate some SSH keys for the boxes:

```
ssh-keygen -t rsa -b 4096 -C ""
```

Keep hitting enter until files exist in `~/.ssh`.

1. Find the public key for `etcd1` and `etcd2` like so:

```
cat ~/.ssh/id_rsa.pub
```

1. Copy the output for each VM and paste into `etcd0`'s `~/.ssh/authorized_keys` file. This will permit `etcd1` and `etcd2` to SSH in to the VM.

### generate the certs

1. On `etcd1` and `etcd2`, run the following:

```
mkdir ~/certs
cd ~/certs
scp root@<etcd1-ip-address>:/root/certs/ca.pem .
scp root@<etcd1-ip-address>:/root/certs/ca-key.pem .
```

1. On all VMs, run the following:

```
cfssl print-defaults csr > config.json
sed -i '0,/CN/{s/example\.net/'"$PEER_NAME"'/}' config.json
sed -i 's/www\.example\.net/'"$PRIV_IP"'/' config.json
sed -i 's/example\.net/'"$PUB_IP"'/' config.json

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=server config.json | cfssljson -bare server
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=peer config.json | cfssljson -bare peer
```

This will result in the following files at a minimum: `peer.pem`, `peer-key.pem`, `server.pem`, `server-key.pem`.

### set up systemd

1. Now that all the certificates have been generated, you will now install and set up etcd on each VM. First install etcd binaries like so:

```
export ETCD_VERSION=v3.2.8
wget -q --show-progress --https-only --timestamping \
  "https://github.com/coreos/etcd/releases/download/$ETCD_VERSION/etcd-$ETCD_VERSION-linux-amd64.tar.gz"
tar -xvf etcd-$ETCD_VERSION-linux-amd64.tar.gz
sudo mv etcd-$ETCD_VERSION-linux-amd64/etcd* /usr/local/bin/
rm -rf etcd-v3.2.10-linux-amd64*
```

1. Next, copy the systemd unit file like so:

```
cat >/etc/systemd/system/etcd.service <<EOL
[Unit]
Description=etcd
Documentation=https://github.com/coreos/etcd
Conflicts=etcd.service
Conflicts=etcd2.service

[Service]
EnvironmentFile=/etc/etcd.env
Type=notify
Restart=always
RestartSec=5s
LimitNOFILE=40000
TimeoutStartSec=0

ExecStart=/usr/local/bin/etcd --name ${PEER_NAME} \
    --data-dir /var/lib/etcd \
    --listen-client-urls https://${PUBLIC_IP}:2379 \
    --advertise-client-urls https://${PUBLIC_IP}:2379 \
    --listen-peer-urls https://${PRIVATE_IP}:2380 \
    --initial-advertise-peer-urls https://${PRIVATE_IP}:2380 \
    --cert-file=/root/certs/server.pem \
    --key-file=/root/certs/server-key.pem \
    --client-cert-auth \
    --trusted-ca-file=/root/certs/ca.pem \
    --peer-cert-file=/root/certs/peer.pem \
    --peer-key-file=/root/certs/peer-key.pem \
    --peer-client-cert-auth \
    --peer-trusted-ca-file=/root/certs/ca.pem \
    --initial-cluster etcd0=https://<etcd0-ip-address>:2380,etcd1=https://<etcd1-ip-address>:2380,etcd1=https://<etcd2-ip-address>:2380 \
    --initial-cluster-token my-etcd-token \
    --initial-cluster-state new

[Install]
WantedBy=multi-user.target
EOL
```

Make sure you replace `<etcd0-ip-address>`, `<etcd1-ip-address>` and `<etcd2-ip-address>` will the real VM IPv4s.

1. Finally, launch etcd like so:

```
systemctl daemon-reload
systemctl start etcd
```

1. Check that it launched successfully:

```
systemctl status etcd
```

## Provision master nodes

1. You will need to provision 3 Virtual Machines for your masters. For the sake of simplicity, we will name these instances `master0`, `master1` and `master2`.

## Set up master LB

1. The next step is to create a cloud Load Balancer for your master nodes. This solution will depend on your cloud provider. Examples are:

- [AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)
- [GCE Load Balancing](https://cloud.google.com/compute/docs/load-balancing/)
- [Azure](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview)

You will need to ensure that the load balancer routes to all three master VMs set up in the previous step **on port 6443**. If possible, use a smart load balancing algorithm like "least connections", and use health checks so unhealthy nodes can be removed from circulation. Most providers will provide these features.

## Set up master VMs

1. On each master VM, run the following:

```
cat >install.sh <<EOL
apt-get update && apt-get install -y docker.io apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
EOL
chmod +x ./install.sh
./install.sh
```

This will install of the dependencies required for kubeadm to run.

### acquire etcd certs

1. Generate SSH keys for each of the master nodes by following the steps in the [create ssh access](#create-ssh-access) section. After doing this, each master will have an SSH key and will be permitted to SSH into `etcd0`.

1. Next, run the following:

```
mkdir -p /etc/kubernetes/pki/etcd
scp root@<etcd1-ip-address>:/root/certs/ca.pem /etc/kubernetes/pki/etcd
scp root@<etcd1-ip-address>:/root/certs/client.pem /etc/kubernetes/pki/etcd
scp root@<etcd1-ip-address>:/root/certs/client-key.pem /etc/kubernetes/pki/etcd
```

### use kubeadm

1. The next step is to write the configuration file for kubeadm:

```
cat >config.yaml <<EOL
apiVersion: kubeadm.k8s.io/v1alpha1
kind: MasterConfiguration
api:
  advertiseAddress: <load-balancer-ip>
etcd:
  endpoints:
  - https://<etcd0-ip>:2379
  - https://<etcd1-ip>:2379
  - https://<etcd2-ip>:2379
  caFile: /etc/kubernetes/pki/etcd/ca.pem
  certFile: /etc/kubernetes/pki/etcd/client.pem
  keyFile: /etc/kubernetes/pki/etcd/client-key.pem
networking:
  podSubnet: 10.244.0.0/16
apiServerCertSANs:
- <load-balancer-ip>
EOL
```

Ensure that `<load-balancer-ip>` is replaced with the public IPv4 of your load balancer. You will also need to replace `<etcd0-ip>`, `<etcd1-ip>` and `<etcd2-ip>` with the IP addresses of your three etcd nodes.

**Note**: We are assuming that Flannel will be the CNI provider. If you want to use another provide like Weave, please read the [requirements section](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network) and adjust the configuration file accordingly.

1. When this is done, run kubeadm like so:

```
kubeadm init --config=config.yaml
```

## checkpointing

To ensure fault tolerance and proper disaster recovery handling, you will need to checkpoint critical pods. Checkpointing saves the pod manifests on disk and restores them in the event of a node reboot or power outage.

## install workers

1. The final step is to provision and set up the worker nodes. To do this, you will need to provision at least 3 Virtual Machines.
1. To configure the worker nodes, you [follow the same steps](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#44-joining-your-nodes) as non-HA workloads.