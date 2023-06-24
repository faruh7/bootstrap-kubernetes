## Documentation link:

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/ 

## Step 1: Setup containerd

```bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```
```bash
modprobe overlay
modprobe br_netfilter
```
```bash
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```
```bash
sysctl --system
```
```bash
apt-get install -y containerd
mkdir -p /etc/containerd
containerd config default > /etc/containerd/config.toml
```
```bash
nano /etc/containerd/config.toml
```

## --> SystemdCgroup = true
```bash
systemctl restart containerd
```

## Step 2: Kernel Parameter Configuration
```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```
```bash
sudo sysctl --system
```

## Step 3: Configuring Repo and Installation
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```bash
sudo apt-get update
apt-cache madison kubeadm
sudo apt-get install -y kubelet=1.22.0-00 kubeadm=1.22.0-00 kubectl=1.22.0-00
sudo apt-mark hold kubelet kubeadm kubectl
```

## Step 4: Initialize Cluster with kubeadm (Only master node)
```bash
kubeadm init --pod-network-cidr=10.244.0.0/16
```
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step 5: Install Network Addon (flannel) (master node)
```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## Step 6: Upgrade Kubeadm Cluster
```bash
apt-cache madison kubeadm
apt-mark unhold kubelet kubectl kubeadm
apt-get update && apt-get install -y kubelet=1.23.7-00 kubectl=1.23.7-00 kubeadm=1.23.7-00
apt-mark hold kubelet kubectl
kubeadm version
kubeadm upgrade plan
kubeadm upgrade apply 1.23.0
systemctl daemon-reload
systemctl restart kubelet
```


