# atelier07

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-07`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh 
vagrant ssh ansible
```

Nous allons installer successivement les paquets `tree`, `git` et `nmap` sur toutes les cibles.

```sh
ansible all -m package -a "name=tree,git,nmap"
```

> Voici le résultat de la commande précédente :

```sh
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap"
rocky | CHANGED => {
    "changed": true,
    "msg": "",
    "rc": 0,
    "results": [
        "Installed: git-core-doc-2.43.5-2.el9_5.noarch",
        "Installed: perl-Error-1:0.17029-7.el9.noarch",
        "Installed: nmap-ncat-3:7.92-3.el9.x86_64",
        "Installed: nmap-3:7.92-3.el9.x86_64",
```

Lorsqu'on execute à nouveau, nous obtenons le résultat `"changed": false`.

```sh
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap"
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree",
        "git",
        "nmap"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
```
Nous allons maintenant désinstaller les paquets précédemment installés.

```sh
ansible all -m package -a "name=tree,git,nmap state=absent"
```

> Voici le résultat de la commande précédente :

```sh
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap state=absent"
suse | CHANGED => {
    "changed": true,
    "cmd": [
        "/usr/bin/zypper",
        "--quiet",
```

Lorsqu'on execute à nouveau la commande, nous obtenons le résultat `"changed": false`.

```sh
[vagrant@ansible ema]$ ansible all -m package -a "name=tree,git,nmap state=absent"
debian | SUCCESS => {
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree",
        "git",
        "nmap"
    ],
    "rc": 0,
    "state": "absent",
    "update_cache": false
}
```

Copions le fichier `/etc/fastab` du contrôleur vers toutes les cibles sous forme d'un fichier `/tmp/test3.txt`.
