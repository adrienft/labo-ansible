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

Nous allons ensuite modifier le fichier `/etc/hosts` en associant les adresses IP des machines cibles à leurs noms d'hôtes.

```sh
sudo nano /etc/hosts
```

> Retrouvez ci-dessous les lignes à ajouter au fichier :

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

Générons maintenant une clé SSH sur le contrôleur.

```sh
ssh-keygen
```

Enfin, nous allons distribuer notre clé publique aux différents hôtes cibles.
Le mot de passe par défaut des machines virtuelles est `vagrant`.

```sh
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

Installons Ansible en utilisant les dépôts d'Ubuntu.
Notons toutefois qu'il s'agira d'une ancienne version (sans incidence pour cet exercice).

```sh
sudo apt update
sudo apt install ansible -y
```

Nous pouvons à présent tester notre configuration.

```sh
ansible all -i target01,target02,target03 -u vagrant -m ping
```

> Retrouvez le résultat de la commande précédente ci-dessous :

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

Nous allons créer un répertoire de travail pour notre projet Ansible.

```sh
mkdir ~/monprojet
```

puis créer un fichier `ansible.cfg` dans ce même répertoire. 

```sh
touch ~/monprojet/ansible.cfg
```

Vérifions maintenant que le fichier a bien été pris en compte par Ansible.

```sh
cd  ~/monprojet/
ansible --version | head -n 2
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
vagrant@control:~/monprojet$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```

Modifions le fichier `ansible.cfg` pour y intégrer un fichier d'inventaire. 

```sh
nano  ansible.cfg
```

> Retrouvez ci-dessous les lignes à ajouter au fichier :

```sh
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

Nous pouvons maintenant tester la journalisation.

```sh
ansible all -i target01,target02,target03 -u vagrant -m ping
cat ~/journal/ansible.log
```

> Retrouvez le résultat de la commande précédente ci-dessous :

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

Nous allons à présent créer notre fichier d'inventaire et créer un groupe `[testlab]` en y intégrant nos trois machines cibles.
Profitons-en pour définir explicitement l'utilisateur `vagrant` et l'élévation de privilèges.

```sh
nano hosts
```

> Retrouvez ci-dessous les lignes à ajouter au fichier :  :

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

> Retrouvez le résultat de la commande précédente ci-dessous :

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

Nous allons afficher la première ligne du fichier `/etc/shadow` de tous les hôtes cibles.

```sh
ansible all -a "head -n 1 /etc/shadow"
```

> Retrouvez le résultat de la commande précédente ci-dessous :

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
vagrant destroy -f
```
