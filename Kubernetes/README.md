# Kubernetes

I'll perform the next steps manually and will later create Ansible playbooks. I will use Flannel as pod network add-on.

## Initialize master
SSH into Master

`ssh pi@192.168.178.10`

Pull images for kubeadm

`sudo kubeadm config images pull`

Initialize master

`sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.244.0.0/16`

As stated in the output, run the following commands as the regular Pi user

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Save the join command for use on the cluster nodes.

`kubeadm join 192.168.178.10:6443 --token ia7kv4.b1ll2jfk2px14fsm --discovery-token-ca-cert-hash sha256:364ba409f94f328b069c62c2820e6448e685915cbb5a43c226076285f9e5b17c`

Check pods

`kubectl get pods --all-namespaces`

Install Flannel

`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`

## Join nodes

SSH into Node 1 and 2

`ssh pi@192.168.178.11` (.12)

Join cluster

`sudo kubeadm join 192.168.178.10:6443 --token ia7kv4.b1ll2jfk2px14fsm --discovery-token-ca-cert-hash sha256:364ba409f94f328b069c62c2820e6448e685915cbb5a43c226076285f9e5b17c`

Run `kubectl get nodes` on the master to verfiy the correct status.

```
NAME       STATUS   ROLES    AGE   VERSION
kubepi01   Ready    master   14m   v1.13.1
kubepi02   Ready    <none>   92s   v1.13.1
kubepi03   Ready    <none>   70s   v1.13.1
```

## Build an application
[https://kubernetes.io/docs/tutorials/]

## Resources
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
