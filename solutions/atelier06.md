# atelier06

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-06`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh 
vagrant ssh control
```

Nous allons modifier le fichier `/etc/hosts` en associant les adresses IP cibles à des noms d'hôtes.

```sh
sudo nano /etc/hosts
```

> Voici donc les lignes à ajouter au fichier :

```sh
192.168.56.10  control.sandbox.lan     control
192.168.56.20  target01.sandbox.lan    target01
192.168.56.30  target02.sandbox.lan    target02
192.168.56.40  target03.sandbox.lan    target03
```

Collectons les clés SSH publiques des hôtes cibles.

```sh
ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
```

Générons une clé SSH sur le contrôleur.

```sh
ssh-keygen
```

Enfin, nous allons distribuer notre clé publique aux différents hôtes cibles.
Le mot de passe des machines virtuelles est `vagrant`.

```sh
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```
Installons maintenant Ansible de façon simple en utilisant les dépots de Ubuntu.
Il s'agit par contre d'une ancienne version (sans incidence pour cet exercice).

```sh
sudo apt update
sudo apt install ansible -y
```

Nous pouvons à présent tester notre configuration précédente.

```sh
ansible all -i target01,target02,target03 -u vagrant -m ping
```

> Voici le résultat de la commande ci-dessus :

```sh
 vagrant@control:~$ ansible all -i target01,target02,target03 -u vagrant -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Nous allons créer un répertoire de projet nommé `monprojet`.

```sh
mkdir ~/monprojet
```

puis créer un fichier `ansible.cfg` dans ce même dossier. 

```sh
touch ~/monprojet/ansible.cfg
```

Vérifions que le fichier ait bien été pris en compte par Ansible.

```sh
cd  ~/monprojet/
ansible --version | head -n 2
```

> Voici le résultat de la commande ci-dessus :

```sh
vagrant@control:~/monprojet$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```

Modifions le fichier `ansible.cfg` pour y intégrer un fichier d'inventaire. 

```sh
nano  ansible.cfg
```

> Voici le contenu à intégrer dans le fichier précedent : 

```sh
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

Nous pouvons maintenant tester la journalisation. 

```sh
ansible all -i target01,target02,target03 -u vagrant -m ping> Voici le contenu à intégrer dans le fichier précedent : 

```sh
[defaults]
inventory = ./hosts
log_path = ./ansible.log
```

cat ~/journal/ansible.log
```

> Voici le résultat obtenu : 

```sh 
vagrant@control:~/monprojet$ cat ~/journal/ansible.log 
2025-02-12 10:32:15,266 p=4140 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:32:15,288 p=4140 u=vagrant n=ansible | target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:32:15,307 p=4140 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Nous allons à présent créer notre fichier d'inventaire en créer un groupe `[testlab]` et en y intégrant nos trois cibles.
Profitons en pour définir explicitement l'utilisateur `vagrant` pour la connexion aux cibles ainsi que l'élévation de privilèges.

```sh
nano hosts
```

> Voici les lignes à ajouter dans le fichier `hosts` : 

```sh
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
ansible_become=yes
```

Testons notre configuration. 

```sh
ansible all -m ping
```
> Voici le résultat obtenu :

```sh
vagrant@control:~/monprojet$ ansible  all -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Nous allons à présent afficher la première ligne du fichier `/etc/shadow` sur tous les hôtes cibles.

```sh
ansible all -a "head -n 1 /etc/shadow"
```

> Voici le résultat obtenu :

```sh
vagrant@control:~/monprojet$ ansible all -a "head -n 1 /etc/shadow"
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```

Cet atelier est désormais terminé.
Faisons un peu de nettoyage.

```sh
exit 
vagrant destroy -f```
