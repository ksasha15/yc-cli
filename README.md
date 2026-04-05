# yc-cli

### Используя Ya CLI, написать скрипт используя bash для создания инфраструктуры, аналогичной созданной в прошлом занятии.

#### Подготовка:
```
$ yc init
Welcome! This command will take you through the configuration process.
Pick desired action:
[1] Re-initialize this profile 'default' with new settings
[2] Create a new profile
Please enter your numeric choice: 1
Please go to https://oauth.yandex.ru/authorize?response_type=token&client_id=<censored> in order to obtain OAuth token.
Please enter OAuth token: <censored>
You have one cloud available: 'cloud-askosenok' (id = b1gg5asq7t335buha16j). It is going to be used by default.
Please choose folder to use:
[1] default (id = b1ga4rba005norl7p6pr)
[2] Create a new folder
Please enter your numeric choice: 1
Your current folder has been set to 'default' (id = b1ga4rba005norl7p6pr).
Do you want to configure a default Compute zone? [Y/n] Y
Which zone do you want to use as a profile default?
[1] ru-central1-a
[2] ru-central1-b
[3] ru-central1-d
[4] ru-central1-k
[5] Don't set default zone
Please enter your numeric choice: 3
Your profile default Compute zone has been set to 'ru-central1-d'.
u24@Ubuntu22:~$
u24@Ubuntu22:~$ yc vpc network --help
```
#### Скрипт:
```
u24@Ubuntu22:~$ yc vpc network create --name internal-bastion-network 
yc vpc subnet create --name internal-bastion-subnet --range 172.16.16.0/24 --network-name internal-bastion-network
yc vpc network create --name external-bastion-network 
yc vpc subnet create --name external-bastion-subnet --range 192.168.0.0/24 --network-name external-bastion-network
yc compute instance create --name bastion --network-interface subnet-name=external-bastion-subnet,nat-ip-version=ipv4 --zone ru-central1-d --ssh-key ~/.ssh/ssh-key-1771855316608.pub
```
#### Проверка:
```
yc compute instance get bastion
----output omitted-------
one_to_one_nat:
address: 158.160.224.213

u24@Ubuntu22:~$ ssh yc-user@158.160.224.213
The authenticity of host '158.160.224.213 (158.160.224.213)' can't be established.
ED25519 key fingerprint is SHA256:0DVQxeDktGpILYBmLQGc3QVIzv+YgQUQCsophODEZ7Y.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '158.160.224.213' (ED25519) to the list of known hosts.^C
```
#### Удаление инфраструктуры
```
yc compute instance delete bastion
yc vpc subnet delete internal-bastion-subnet
yc vpc subnet delete external-bastion-subnet
yc vpc subnet delete external-bastion-network
yc vpc network delete external-bastion-network
yc vpc network delete internal-bastion-network
```
