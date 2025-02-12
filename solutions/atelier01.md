# atelier01

## Exercice 1

Nous allons commencer par démarrer la VM `ubuntu` depuis le répertoire `atelier-01`.

```sh
vagrant up ubuntu
```

Connectons-nous à la machine virtuelle.

```sh 
vagrant ssh ubuntu
```

Rafraichissons maintenant les informations sur les paquets.

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

Une fois Ansible installé, nous pouvons vérifier sa version.

```sh
ansible --version
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
vagrant@ubuntu:~$ ansible --version
ansible 2.10.8
  config file = None
```

Déconnectons-nous et détruisons la machine virtuelle.

```sh
exit
vagrant destroy -f ubuntu
```

## Exercice 2

Nous allons commencer par démarrer la VM `ubuntu` depuis le répertoire `atelier-01`.

```sh
vagrant up ubuntu
```
Connectons-nous à la machine virtuelle.

```sh
vagrant ssh ubuntu
```

Rafraichissons maintenant les informations sur les paquets.

```sh
sudo apt update
```

...et ajoutons un nouveau dépôt.

```sh
sudo apt-add-repository ppa:ansible/ansible
```

Installons à présent le paquet `ansible`.

```sh
sudo apt install -y ansible
```

Une fois Ansible installé, nous pouvons vérifier sa version.

```sh
ansible --version
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```
vagrant@ubuntu:~$ ansible --version
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
```
La version d'Ansible installée avec le dépôt PPA est plus récente que celle du dépôt de base.

Déconnectons-nous et détruisons la machine virtuelle.

```sh
exit
vagrant destroy -f ubuntu
```

## Exercice 3

Nous allons commencer par démarrer la VM `rocky` depuis le répertoire `atelier-01`.

```sh
vagrant up rocky
```

Connectons-nous à la machine virtuelle.

```sh
vagrant ssh rocky
```

Installons un nouveau dépôt, nommé `epel-release`.

```sh
sudo dnf install -y epel-release
```

Passons à l'installation de `python3`.

```sh
sudo dnf install -y python3-pip
```

Une fois l'installation terminée, nous pouvons créer un environnement virtuel nommé `ansible`.

```sh
python3 -m venv ~/.venv/ansible
```

Démarrons l'environnement virtuel.

```sh
source ~/.venv/ansible/bin/activate
```

Mettons à jour le gestionnaire de paquets `pip`.

```sh
pip install --upgrade pip
```

Nous allons à présent installer `ansible` avec le gestionnaire de paquets Python.

```sh
pip install ansible
```

Vérifions la version de Ansible.

```sh
ansible --version
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```
(ansible) [vagrant@rocky ~]$ ansible --version
ansible [core 2.15.13]
  config file = None
```

Déconnectons-nous et détruisons la machine virtuelle.

```sh
exit
vagrant destroy -f rocky
```
