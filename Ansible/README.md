# Ansible

## Install
[https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu]

## Get started
[https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html]

## After Raspbian setup
Copy SSH keys to Raspberrys.
`ssh-copy-id -i .ssh/id_rsa.pub pi@192.168.178.10` (.11, .12)

Modify `/etc/ansible/hosts` and add the new Raspberrys to a group named `kubepi`.
```
[kubepi]
192.168.178.[10:12]

[kubepi:vars]
ansible_user=pi
```

A couple of test.

```
ansible kubepi -m ping
ansible kubepi -a "uptime"
ansible kubepi -a "poweroff" -b
ansible kubepi -a "reboot" -b
```

## Run playbooks
Update Raspberrys

`ansible-playbook install_updates.yml`

Create SD card image or TAR backups on NFS share

```
ansible-playbook run_dd_backup.yml
ansible-playbook run_tar_backup.yml
```

Install and configure requirements for Kubernetes
```
ansible-playbook install_req.yml
```

Install Kubernetes
```
ansible-playbook install_kube.yml
```


## Continue with
[Kubernetes](https://github.com/vzovko/KubePi/tree/master/Kubernetes)

## Resources
* [https://docs.ansible.com/ansible/latest/index.html]
* [https://www.ansible.com/resources/webinars-training/introduction-to-ansible]
* [https://kubernetes.io/docs/setup/independent/install-kubeadm/]

