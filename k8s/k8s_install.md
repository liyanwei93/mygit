
## Begin

swapoff -a

## Docker install 
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce
sudo systemctl start docker

## cri-dockerd install

```
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd-0.3.4.amd64.tgz
tar -xf cri-dockerd-0.3.4.amd64.tgz
cp cri-dockerd/cri-dockerd /usr/local/bin/cri-dockerd

rm -rf cri-dockerd
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
install packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable --now cri-docker.socket
```

### Install kubeadm, kubelet, kubectl

cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

sudo systemctl enable --now kubelet

### Master node

kubeadm init --cri-socket=unix:///var/run/cri-dockerd.sock --pod-network-cidr=192.168.0.0/16
//kubeadm reset --cri-socket=unix:///var/run/cri-dockerd.sock 

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml
watch kubectl get pods -n calico-system
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
kubectl taint nodes --all node-role.kubernetes.io/master-

kubectl get nodes -o wide

Then you can join any number of worker nodes by running the following on each as root:

kubeadm token create --print-join-command

### Worker node

kubeadm join 9.30.116.11:6443 --token  --discovery-token-ca-cert-hash  --cri-socket=unix:///var/run/cri-dockerd.sock
