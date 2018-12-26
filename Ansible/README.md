# Ansible

## Install
[https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu]

## Get started
[https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html]

## After Raspbian setup
Copy SSH Keys to Raspberrys.
`ssh-copy-id -i .ssh/id_rsa.pub pi@192.168.178.10`

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

`ansible-playbook "/home/valentin/Projects/KubePi/Ansible/install_updates.yml"`

## Resources
* [https://docs.ansible.com/ansible/latest/index.html]
* [https://www.thomas-krenn.com/de/wiki/OpenSSH_Public_Key_Authentifizierung_unter_Ubuntu]
