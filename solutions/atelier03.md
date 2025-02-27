# atelier03

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-03`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh 
vagrant ssh control
```

Ansible est déjà installé sur la machine.

```sh
type ansible
```

Nous allons à présent modifier le fichier `/etc/hosts` en associant les adresses IP des machines cibles à leurs noms d'hôtes.

```sh
sudo vi /etc/hosts
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

Nous pouvons à présent tester notre configuration.

```sh
ansible all -i target01,target02,target03 -u vagrant -m ping
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
vagrant@control:~$ ansible all -i target01,target02,target03 -u vagrant -m ping
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
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
```

Place au nettoyage.

```sh
exit
vagrant destroy -f
```
