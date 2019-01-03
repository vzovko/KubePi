# Kubernetes

I'll perform the next steps manually and will later create Ansible playbooks. I will use Flannel as pod network add-on.

## Initialize master
SSH into the master.

`ssh pi@192.168.178.10`

Pull images for kubeadm.

`sudo kubeadm config images pull`

Initialize master.

`sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.244.0.0/16`

As stated in the output, run the following commands as the regular Pi user.

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Save the join command for use on the cluster nodes.

`kubeadm join 192.168.178.10:6443 --token ia7kv4.b1ll2jfk2px14fsm --discovery-token-ca-cert-hash sha256:364ba409f94f328b069c62c2820e6448e685915cbb5a43c226076285f9e5b17c`

Check pods.

`kubectl get pods --all-namespaces`

Install Flannel.

`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`

## Join nodes

SSH into node 1 and 2.

`ssh pi@192.168.178.11` (.12)

Join the cluster.

`sudo kubeadm join 192.168.178.10:6443 --token ia7kv4.b1ll2jfk2px14fsm --discovery-token-ca-cert-hash sha256:364ba409f94f328b069c62c2820e6448e685915cbb5a43c226076285f9e5b17c`

Run `kubectl get nodes` on the master to verfiy the correct status.

```
NAME       STATUS   ROLES    AGE   VERSION
kubepi01   Ready    master   14m   v1.13.1
kubepi02   Ready    <none>   92s   v1.13.1
kubepi03   Ready    <none>   70s   v1.13.1
```

## Configure Admin VM to access the cluster
Create `.kube` folder in home dir and scp kubeconfig from master to admin vm.

```
mkdir ~/.kube
scp pi@192.168.178.10:/home/pi/.kube/config ~/.kube/config
```

Install kubectl on admin vm with `sudo snap install kubectl --classic`.

Verfiy connectivity to cluster with `kubectl get pods --all-namespaces` and/or `kubectl get nodes`.

## Install Dashboard
[https://github.com/kubernetes/dashboard/wiki/Installation]

[https://github.com/kubernetes/dashboard/wiki/Access-control#admin-privileges]

Grant full admin privileges to dashboard's service account (Development Release for ARM).

`kubectl create -f dashboard-admin.yaml`

Deploy dashboard for ARM.

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard-arm-head.yaml`

Check with `kubectl get pods --all-namespaces` that the pod `kubernetes-dashboard-head` is running.

Run `kubectl proxy` to start the local proxy server.

Open URL `http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard-head:/proxy/#/login`.

Skip login to use default service account.

**Nothing works. Wtf?**

[https://github.com/kubernetes/dashboard/wiki/Compatibility-matrix]

Remove it.
```
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard-arm-head.yaml
kubectl delete -f dashboard-admin.yaml
```

## NFS Dynamic Provisioner for Persitent Volume Claims
[https://github.com/kubernetes-incubator/external-storage/blob/master/nfs-client/README.md]

Clone [external-storage](https://github.com/kubernetes-incubator/external-storage) repository and open the subfolder `nfs-client`.

```
git clone https://github.com/kubernetes-incubator/external-storage.git
cd external-storage/nfs-client/
```

Setup authorization. *(Currently not sure, if this is neccessary in this installation)*

`kubectl create -f deploy/rbac.yaml`

Modify `deploy/deployment-arm.yaml` and change the values for `PROVISIONER_NAME`, `NFS_SERVER` and `NFS_PATH`.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
---
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nfs-client-provisioner
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccount: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner-arm:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: nfs-storage
            - name: NFS_SERVER
              value: 192.168.178.30
            - name: NFS_PATH
              value: /volume1/kube-data/pv
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.178.30
            path: /volume1/kube-data/pv

```

Modify `/deploy/class.yaml` and change value for `provisioner` to the `nfs-storage`.

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: nfs-storage
parameters:
  archiveOnDelete: "false"
```

Setup the nfs provisioner.

```
kubectl create -f deploy/deployment-arm.yaml
kubectl create -f deploy/class.yaml
```

Verfiy new StorageClass.
```
kubectl get storageclass

NAME                  PROVISIONER   AGE
managed-nfs-storage   nfs-storage   3m1s

```

**Test Deployment failed, SUCCESS file not created**


## Build an application
[https://kubernetes.io/docs/tutorials/]


## Resources
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
* [https://kubernetes.io/docs/reference/kubectl/cheatsheet/]
