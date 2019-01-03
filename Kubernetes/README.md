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

`sudo kubeadm join 192.168.178.10:6443 --token a2zday.lvpn6yad18ktq8u2 --discovery-token-ca-cert-hash sha256:36e4f87d2ce16000298b63d41e379da32dcd19abc2f7c9ed6a6201bc024fc885`

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
[https://github.com/kubernetes/dashboard/wiki/Creating-sample-user]

Deploy dashboard.

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard-arm.yaml`

Check with `kubectl get pods --all-namespaces` that the pod `kubernetes-dashboard` is running.

Create admin user and role binding for dashboard user.

`kubectl create -f admin-user.yaml -f admin-role.yaml`

Retreive authentication token for login.

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

Name:         admin-user-token-q6q9q
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 1cb5e912-0f42-11e9-9cfe-b827ebe1339e

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXE2cTlxIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIxY2I1ZTkxMi0wZjQyLTExZTktOWNmZS1iODI3ZWJlMTMzOWUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.mqFnRhCHhoV0fLln0GzOleuQnG4BpBP5GZq64bdamZLhxhilVMl1Hp0C5pujIuc0X68QiXoj8wc6FrDCO0Yhgdu2aBiHPTB_-yDvUBMYkpBK6QZZwr4-Yh0Vs5jrrALL__ACrWqgB2Aj03hYo_VzQ6fhv8MCVY7DHhqmopg3JS66fU0dWxTDfL_kzgjw_LPQe6IIgnrpuSIH8e2oJG_HvIdwqMG7uR8oyhrMBOzQ1r8-N4yt-5osNpq87a_oeKms10IQBLWb7mwtliykgDMOXgZeYDK3-kO6cT7-TCjDiBtXMJZZiwUobMP9e8LDqe-Z1zbZkc9uuZeRPnv6DGdQzw
```

Run `kubectl proxy` to start the local proxy server.

Open URL `http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/` and log in with the token.

![Dashboard Login](../Images/dash1.png)

![Dashboard Overview](../Images/dash2.png)

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
