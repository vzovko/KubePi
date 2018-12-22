# KubePi

## Admin Machine
* Ubuntu 18 VM
* Visual Studio Code
* PowerShell Core
* APT Packages
  * open-vm-tools-desktop, curl, git
* Generate SSH key [https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/] and add public key to account. Key will later be used for Ansible.

Create SSH key: `ssh-keygen -t rsa -b 4096`

## NEXT
* SSH keys zu Pis
* Ansible test

## To Do
* Raspbian
* Kubernetes
* Pihole Container
* Logging & Monitoring (Fluentd, Elk, Prometheus, Grafana?)


## Resources
* [https://blog.hypriot.com/post/setup-kubernetes-raspberry-pi-cluster/]
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
