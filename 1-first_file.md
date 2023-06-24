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

