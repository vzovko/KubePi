# KubePi
This repo is currently considered to be a dumping ground for all the things I want to try out with my Raspberry Pi cluster which ultimately should run Kubernetes and a couple of containers at home. Besides that I want to try out a couple of other things like Ansible and stuff.

![Raspberry Pi Cluster](Images/img1.png)

## Admin Machine
* Ubuntu 18 VM
* APT Packages
  * open-vm-tools-desktop, curl, git
* Additional Tools
  * Visual Studio Code [https://code.visualstudio.com/docs/setup/linux#_installation]
  * PowerShell Core [https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6#installation-via-package-repository---ubuntu-1804]

## Generate SSH key for GitHub an Ansible

Generate SSH key [https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/] and add public key to account. Key will later be used for Ansible.

Generate SSH key: `ssh-keygen -t rsa -b 4096 -C "my@mail.com"`

## Continue with
[Raspbian Setup](/tree/master/Raspbian/)

## To Do
* Kubernetes
* Pihole Container
* Logging & Monitoring (Fluentd, Elk, Prometheus, Grafana?)

## Resources
* [https://blog.hypriot.com/post/setup-kubernetes-raspberry-pi-cluster/]
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
* [https://gist.github.com/alexellis/fdbc90de7691a1b9edb545c17da2d975]
* [https://kubecloud.io/setting-up-a-kubernetes-1-11-raspberry-pi-cluster-using-kubeadm-952bbda329c8]
