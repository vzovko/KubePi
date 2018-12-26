# KubePi

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
[Raspbian Setup](Raspbian/README.md)

## To Do
* Raspbian
* Kubernetes
* Pihole Container
* Logging & Monitoring (Fluentd, Elk, Prometheus, Grafana?)


## Resources
* [https://blog.hypriot.com/post/setup-kubernetes-raspberry-pi-cluster/]
* [https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/]
