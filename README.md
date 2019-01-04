# KubePi
This repo is currently considered to be a dumping ground for all the things I want to try out with my Raspberry Pi cluster which ultimately should run Kubernetes and a couple of containers at home. Besides that I want to try out a couple of other things like Ansible and stuff.

I did not come up with all this by my own. Please refer to Alex Ellis [k8s-on-raspbian](https://github.com/alexellis/k8s-on-raspbian) repo for setting up Kubernetes on Raspbian and the [rak8s](https://github.com/rak8s/rak8s) repo for setting up Kubernetes on Raspian with Ansible.

![Raspberry Pi Cluster](Images/img1.png)

## Admin Machine
* Ubuntu 18 VM
* APT Packages
  * open-vm-tools-desktop, curl, git, tmux
* Additional Tools
  * Visual Studio Code [https://code.visualstudio.com/docs/setup/linux#_installation]
    * Extensions: Ansible, Bracket Pair Colorizer, Kubernetes, PowerShell, TODO Hightlight, Trailing Spaces, YAML.
  * PowerShell Core [https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6#installation-via-package-repository---ubuntu-1804]
  * GitKraken

## Generate SSH key
Generate SSH key on admin vm. I'll use this one for Ansible and GitHub.
```
ssh-keygen -t rsa -b 4096 -C "my@mail.com"
```

## Continue with
[Raspbian Setup](https://github.com/vzovko/KubePi/tree/master/Raspbian)

## To Do
* Kubernetes
* Pihole Container
* Logging & Monitoring (Fluentd, Elk, Prometheus, Grafana?)

## Resources
* [https://github.com/alexellis/k8s-on-raspbian]
* [https://github.com/rak8s/rak8s]

