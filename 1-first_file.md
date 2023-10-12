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
sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```bash
sudo apt-get update
apt-cache madison kubeadm
sudo apt-get install -y kubelet=1.24.0-00 kubeadm=1.24.0-00 kubectl=1.24.0-00 cri-tools=1.24.2-00
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

## Step 6: Connect Worker Node (Only worker node)
```bash
kubeadm join 159.89.165.203:6443 --token qmw5dj.ljdh8r74ce3y85ad \
        --discovery-token-ca-cert-hash sha256:83374ec05088fa7efe9c31cce63326ae7037210ab049048ef08f8c961a048ddf
```

## Step 6: Connect Worker Node (Only worker node)
```bash
kubectl get nodes
kubectl run nginx --image=nginx
kubectl get pods -o wide
```
