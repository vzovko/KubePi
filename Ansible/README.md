# Ansible

## Test Client
Raspberry Pi 2 named `dockerpi` running Raspian with IP `192.168.178.5`. The test client is running multiple containers.

## Install
[https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu]

## Get started
[https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html]

Copy SSH key to client:
`ssh-copy-id -i .ssh/id_rsa.pub pi@192.168.178.5`

SSH Agent:
```
ssh-agent bash
ssh-add ~/.ssh/id_rsa
```

Modify `/etc/ansible/hosts`:
```
[test]
192.168.178.5

[test:vars]
ansible_user=pi
```

Run Ansible test commands:
```
ansible all -m ping -u pi
ansible all -a "/bin/echo hello"
ansible test -m shell -a "docker ps" -b
```

## After inital Raspberry setup
Copy SSH Keys to Raspberrys.
`ssh-copy-id -i .ssh/id_rsa.pub pi@192.168.178.10`

Modify `/etc/ansible/hosts` and add the new Raspberrys a group named `kubepi`.
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