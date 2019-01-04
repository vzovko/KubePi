# Kubernetes

I'll perform the next steps manually and will later create Ansible playbooks. I will use Flannel as pod network add-on.

## Initialize master
SSH into the master.
```
ssh pi@192.168.178.10
```

Pull images for kubeadm.
```
sudo kubeadm config images pull
```

Initialize master.
```
sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.244.0.0/16
```

As stated in the output, run the following commands as the regular Pi user.

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Save the join command for use on the cluster nodes.
```
kubeadm join 192.168.178.10:6443 --token ia7kv4.b1ll2jfk2px14fsm --discovery-token-ca-cert-hash sha256:364ba409f94f328b069c62c2820e6448e685915cbb5a43c226076285f9e5b17c
```

Check pods.
```
kubectl get pods --all-namespaces
```

Install Flannel.
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## Join nodes

SSH into node 1 and 2.
```
ssh pi@192.168.178.11
ssh pi@192.168.178.12
```

Join the cluster.
```
sudo kubeadm join 192.168.178.10:6443 --token a2zday.lvpn6yad18ktq8u2 --discovery-token-ca-cert-hash sha256:36e4f87d2ce16000298b63d41e379da32dcd19abc2f7c9ed6a6201bc024fc885
```

Verfiy the correct status.

```
kubectl get nodes

NAME       STATUS   ROLES    AGE   VERSION
kubepi01   Ready    master   14m   v1.11.6
kubepi02   Ready    <none>   92s   v1.11.6
kubepi03   Ready    <none>   70s   v1.11.6
```

## Configure Admin VM to access the cluster
Create `.kube` folder in home dir on admin vm and scp kubeconfig from master to it.

```
mkdir ~/.kube
scp pi@192.168.178.10:/home/pi/.kube/config ~/.kube/config
```

Install kubectl on admin vm.
```
sudo snap install kubectl --classic
```

Verfiy connectivity to cluster.
```
kubectl get pods --all-namespaces
kubectl get nodes
```

## Install Dashboard
Deploy dashboard.
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard-arm.yaml
```

Check with `kubectl get pods --all-namespaces` that the pod `kubernetes-dashboard` is running.

Create admin user and role binding for dashboard user.
```
kubectl create -f admin-user.yaml
```

Retreive authentication token for login.
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

Start the proxy.
```
kubectl proxy
```

Open the Dashboard URL [http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/] and log in with the token.

![Dashboard Login](../Images/dash1.png)

![Dashboard Overview](../Images/dash2.png)

## Helm
Install Helm on admin vm and initalize it. Use specific Helm tiller image for ARM and a dedicated service account. [https://github.com/jessestuart/tiller-multiarch]

```
sudo snap install helm --classic
kubectl create -f tiller-rbac.yaml
helm init --tiller-image=jessestuart/tiller:v2.9.1 --service-account tiller
```

## Storage
Shared storage is provided by an NFS share on a Synology NAS with IP `192.168.178.30` and path `/volume1/kube-data/pv`.

### Manual Persistent Volumes on NFS Share
Create a subfolder on the NFS share named `pv0001`.

Create a persistent volume with 10 Mb and verfify.
```
kubectl create -f pv0001.yaml
kubectl get pv
```

Create a test pod with an persistent volume claim and verify.
```
kubectl create -f pod0001.yaml
kubectl get pvc,pv,pods
kubectl describe pod pod0001
```

Check the folder `pv0001` to see if a file named `SUCCESS` has been created.

### Install NFS Client Provisioner via Helm for Dynamic Volume Provisioning
Install provisioner and set parameters for NFS server and path, as well as the ARM image to use.
```
helm install --name nfs-prov --set nfs.server=192.168.178.30 --set nfs.path=/volume1/kube-data/pv --set image.repository=quay.io/external_storage/nfs-client-provisioner-arm stable/nfs-client-provisioner

```

Create a test pod with an persitent volume claim and verify.
```
kubectl create -f pod0002.yaml
kubectl get pvc,pv,pods
kubectl describe pod pod0002
```

Check the NFS share folder to see if a subfolder with the name `default-pvc0002-pvc-*` has been created and that it contains a file named `SUCCESS`.

## Networking
There needs to be a load balancer to access applications inside this cluster. I will use MetalLB, because of [this](https://metallb.universe.tf/#why).

### Load Balancer - MetalLB
Install with Helm.
```
helm install --name metallb stable/metallb
```

Modify the IP address range in `metallb-configmap.yaml` and create the configmap afterwards.
```
kubectl create -f metallb-configmap.yaml
```

Deploy an nginx service and pod.
```
kubectl create -f nginx-test.yaml
```

Check if  `service/nginx-test` has an external IP address assigned to it and that `pod/nginx-test` is running.
```
kubectl get svc,pods
NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
service/kubernetes   ClusterIP      10.96.0.1       <none>            443/TCP        1d
service/nginx-test   LoadBalancer   10.100.149.92   192.168.178.200   80:30473/TCP   7m

NAME                                                   READY   STATUS    RESTARTS   AGE
pod/metallb-controller-564dc9b8f9-62q9q                1/1     Running   0          9m
pod/metallb-speaker-7pbcv                              1/1     Running   0          9m
pod/metallb-speaker-k4ghv                              1/1     Running   0          9m
pod/nfs-prov-nfs-client-provisioner-64cd868b87-z5lhn   1/1     Running   3          1d
pod/nginx-test                                         1/1     Running   0          7m

```

Open the external IP in your browser.

![nginx Text](../Images/nginx1.png)

## Todo
Pihole


## Collection of kubectl Commands
```
# Run interactive busybox container
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
```

## Resources
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
* [https://kubernetes.io/docs/reference/kubectl/cheatsheet/]
* [https://github.com/kubernetes/dashboard/wiki/Installation]
* [https://github.com/kubernetes/dashboard/wiki/Access-control#admin-privileges]
* [https://github.com/kubernetes/dashboard/wiki/Creating-sample-user]
* [https://docs.helm.sh/using_helm/#quickstart-guide]
* [https://metallb.universe.tf]
