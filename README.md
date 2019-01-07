# KubePi
This repo is currently considered to be a dumping ground for all the things I want to try out with my Raspberry Pi cluster which ultimately should run Kubernetes and a couple of containers at home. Besides that I want to try out a couple of other things like Ansible and stuff.

I did not come up with all this by my own. Please refer to Alex Ellis [k8s-on-raspbian](https://github.com/alexellis/k8s-on-raspbian) repo for setting up Kubernetes on Raspbian and the [rak8s](https://github.com/rak8s/rak8s) repo for setting up Kubernetes on Raspian with Ansible.

![Raspberry Pi Cluster](Images/img1.png)

## Table of content

* [KubePi](https://github.com/vzovko/KubePi#kubepi)
  * [Admin Machine](https://github.com/vzovko/KubePi#admin-machine)
  * [Generate SSH key](https://github.com/vzovko/KubePi#generate-ssh-key)
* [Rasbian](https://github.com/vzovko/KubePi/tree/master/Raspbian#raspbian)
  * [Hardware](https://github.com/vzovko/KubePi/tree/master/Raspbian#hardware)
  * [Setup](https://github.com/vzovko/KubePi/tree/master/Raspbian#setup)
  * [Inital configuration with raspi-config](https://github.com/vzovko/KubePi/tree/master/Raspbian#inital-configuration-with-raspi-config)
* [Ansible](https://github.com/vzovko/KubePi/tree/master/Ansible#ansible)
  * [Install](https://github.com/vzovko/KubePi/tree/master/Ansible#install)
  * [Configuration and test](https://github.com/vzovko/KubePi/tree/master/Ansible#configuration-and-test)
  * [Run playbooks](https://github.com/vzovko/KubePi/tree/master/Ansible#run-playbooks)
  * [Collection of Ad-Hoc Commands](https://github.com/vzovko/KubePi/tree/master/Ansible#collection-of-ad-hoc-commands)
* [Kubernetes](https://github.com/vzovko/KubePi/tree/master/Kubernetes#kubernetes)
  * [Initialize master](https://github.com/vzovko/KubePi/tree/master/Kubernetes#initialize-master)
  * [Join nodes](https://github.com/vzovko/KubePi/tree/master/Kubernetes#join-nodes)
  * [Configure Admin VM to access the cluster](https://github.com/vzovko/KubePi/tree/master/Kubernetes#configure-admin-vm-to-access-the-cluster)
  * [Install Dashboard](https://github.com/vzovko/KubePi/tree/master/Kubernetes#install-dashboard)
  * [Helm](https://github.com/vzovko/KubePi/tree/master/Kubernetes#helm)
  * [Storage](https://github.com/vzovko/KubePi/tree/master/Kubernetes#storage)
    * [Manual Persistent Volumes on NFS Share](https://github.com/vzovko/KubePi/tree/master/Kubernetes#manual-persistent-volumes-on-nfs-share)
    * [Install NFS Client Provisioner via Helm for Dynamic Volume Provisioning](https://github.com/vzovko/KubePi/tree/master/Kubernetes#install-nfs-client-provisioner-via-helm-for-dynamic-volume-provisioning)
  * [Networking](https://github.com/vzovko/KubePi/tree/master/Kubernetes#networking)
    * [Load Balancer - MetalLB](https://github.com/vzovko/KubePi/tree/master/Kubernetes#load-balancer---metallb)
    * [NodePort Service Example](https://github.com/vzovko/KubePi/tree/master/Kubernetes#nodeport-service-example)
  * [Workloads](https://github.com/vzovko/KubePi/tree/master/Kubernetes#workloads)
    * [Pi-hole](https://github.com/vzovko/KubePi/tree/master/Kubernetes#pi-hole)
  * [Collection of kubectl Commands](https://github.com/vzovko/KubePi/tree/master/Kubernetes#collection-of-kubectl-commands)

## Admin Machine
* Ubuntu 18.04.1 LTS VM
* APT Packages
  * open-vm-tools-desktop, curl, git, tmux
* Additional Tools
  * Visual Studio Code [https://code.visualstudio.com/docs/setup/linux#_installation]
    * Extensions: Ansible, Bracket Pair Colorizer, Kubernetes, PowerShell, TODO Hightlight, Trailing Spaces, YAML.
  * PowerShell Core [https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6#installation-via-package-repository---ubuntu-1804]
  * GitKraken

## Generate SSH key
Generate SSH key on Admin VM. I'll use this one for Ansible and GitHub.
```
ssh-keygen -t rsa -b 4096 -C "my@mail.com"
```

## Continue with
[Raspbian Setup](https://github.com/vzovko/KubePi/tree/master/Raspbian)

## To Do
* Logging & Monitoring

## Resources
* [https://github.com/alexellis/k8s-on-raspbian]
* [https://github.com/rak8s/rak8s]

