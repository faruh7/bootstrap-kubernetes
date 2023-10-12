##### Documentation Link:

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

##### Step 1: Upgrade Kubeadm Cluster
```sh
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