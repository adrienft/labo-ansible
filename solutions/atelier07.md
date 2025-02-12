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

Copions le fichier `/etc/fastab` du contrôleur vers toutes les hôtes cibles sous forme d'un fichier `/tmp/test3.txt`.

```sh
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
```

> Voici le résultat de la commande précédente :

```sh
[vagrant@ansible ema]$ ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
rocky | CHANGED => {
    "changed": true,
    "checksum": "d39263691e31170df199aae3d93f7c556fbb3446",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "md5sum": "b6f1fe0d790a8d2f9b74b95df1c889dc",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 743,
    "src": "/home/vagrant/.ansible/tmp/ansible-tmp-1739367787.2110987-4003-35373851058066/source",
    "state": "file",
    "uid": 0
}
```

Lorsqu'on execute à nouveau la commande, nous obtenons le résultat `"changed": false`. En effet, les configurations ont déjà été appliquées.

```sh
[vagrant@ansible ema]$ ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
debian | SUCCESS => {
    "changed": false,
    "checksum": "d39263691e31170df199aae3d93f7c556fbb3446",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/tmp/test3.txt",
    "size": 743,
    "state": "file",
    "uid": 0
}
```

Supprimons à présent le fichier `/tmp/test3.txt`.

```sh
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```

> Voici le résultat de la commande précédente :

```sh
[vagrant@ansible ema]$ ansible all -m file -a "path=/tmp/test3.txt state=absent"
debian | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
rocky | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
suse | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
```

Lorsqu'on execute à nouveau la commande, nous obtenons le résultat `"changed": false`. En effet, les suppressions ont déjà été appliquées.

```sh
debian | SUCCESS => {
    "changed": false,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
rocky | SUCCESS => {
    "changed": false,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
suse | SUCCESS => {
    "changed": false,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
```

Enfin, nous allons terminer par afficher l'espace utilisé par la partition principale sur tous les Target Hosts.

```sh
ansible all -m command -a "df -h /"
```

> Voici le résultat de la commande précédente :

```sh
debian | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.3G  115G   2% /
suse | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.8G  118G   3% /
rocky | CHANGED | rc=0 >>
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/rl_rocky9-root   70G  2.4G   68G   4% /
```

L'atelier est maintenant terminé. Faisons un peu de nettoyage.

```sh
exit 
vagrant destroy -f
```
