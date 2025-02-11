# atelier01

## Exercice 1

Nous allons commencer par démarrer la VM `ubuntu` depuis le répertoire `atelier-01`.

```sh
vagrant up ubuntu
```

Connectons nous maintenant à la VM.

```sh 
vagrant ssh ubuntu
```

Rafraichissons les informations sur les paquets.

```sh
sudo apt update
```

Recherchons le paquet `ansible` avec les options qui vont bien.

```sh
sudo apt-cache search --names-only ansible 
```

Passons à l'installation du paquet `ansible`.

```sh
sudo apt install -y ansible 
```

Vérifions la version du paquet précédemment installé.

```sh
ansible --version
```
Voici le résultat de la commande ci-dessus :

```sh
vagrant@ubuntu:~$ ansible --version
ansible 2.10.8
  config file = None
```

Déconnectons nous de la machine virtuelle.

```sh
exit
```

Détruisons à présent la VM. 

```sh
vagrant destroy -f ubuntu
```

## Exercice 2

Nous allons commencer par démarrer la VM `ubuntu` depuis le répertoire `atelier-01`.

```sh
vagrant up ubuntu
```
Connectons nous maintenant à la VM.

```sh
vagrant ssh ubuntu
```

Rafraichissons les informations sur les paquets.

```sh
sudo apt update
```

et ajoutons un nouveau dépot.

```sh
sudo apt-add-repository ppa:ansible/ansible
```

Installons le paquet `ansible`.

```sh
sudo apt install -y ansible
```

Vérifions la version du paquet installé.

```sh
ansible --version
```

Voici le résultat de la commande ci-dessus :

```
vagrant@ubuntu:~$ ansible --version
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
```
La version d'Ansible installée avec le dépôt PPA est plus récente que celle du dépôt de base.

Déconnectons nous de la machine virtuelle.

```sh
exit
```

Détruisons à présent la VM.

```sh
vagrant destroy -f ubuntu
```

## Exercice 3

```sh
vagrant up rocky
```  

```sh
vagrant ssh rocky
```

```sh
sudo dnf install -y epel-release 
```

```sh
sudo dnf install -y python3-pip
```

```sh
python3 -m venv ~/.venv/ansible 
```

```sh
source ~/.venv/ansible/bin/activate
```

```sh
pip install --upgrade pip
```

```sh
pip install ansible
```

```sh
pip install --upgrade pip
```

```sh
pip install ansible
```

```sh
ansible --version
```

```
(ansible) [vagrant@rocky ~]$ ansible --version
ansible [core 2.15.13]
  config file = None
```

```sh
exit
```

```sh
vagrant destroy -f ubuntu
```
