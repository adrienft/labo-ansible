# atelier16

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-16`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh
vagrant ssh ansible
```

L'environnement de l'atelier est préconfiguré et prêt à l'emploi.

Rendons-nous dans le répertoire *playbooks* du projet.

```sh
cd ansible/projets/ema/playbooks/
```

Nous allons écrire un premier *playbook* `pkg-info.yml` qui affichera le gestionnaire de paquets utilisé par chaque hôte cible.

```sh
nano pkg-info.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # pkg-info.yml

- hosts: all
  gather_facts: true

  tasks:

    - name: Display package manager
      debug:
        msg: "Le gestionnaire de paquets utilisé est : {{ ansible_pkg_mgr }}"

...
```

Il est l'heure de tester notre livre d'instructions.

```sh
ansible-playbook pkg-info.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] *********************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Display package manager] *************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Le gestionnaire de paquets utilisé est : dnf"
}
ok: [debian] => {
    "msg": "Le gestionnaire de paquets utilisé est : apt"
}
ok: [suse] => {
    "msg": "Le gestionnaire de paquets utilisé est : zypper"
}

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons maintenant écrire un *playbook* `python-info.yml` capable d'afficher la version de Python installée sur chaque cible.

```sh
nano python-info.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # python-info.yml

- hosts: all
  gather_facts: true

  tasks:
    - name: Display Python version
      debug:
        msg: "La version de Python installée est : {{ ansible_python_version }}"

...
```

Il est également possible d'utiliser la variable `{{ ansible_facts.python.version }}` en lieu et place de `{{ ansible_python_version }}`. Notez toutefois que la présentation des résultats ne sera pas la même.

Exécutons notre nouveau livre.

```sh
ansible-playbook python-info.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] *********************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Display Python version] **************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "La version de Python installée est : 3.9.18"
}
ok: [debian] => {
    "msg": "La version de Python installée est : 3.11.2"
}
ok: [suse] => {
    "msg": "La version de Python installée est : 3.6.15"
}

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons terminer cet atelier en écrivant un fichier `dns-info.yml` qui affichera le(s) serveur(s) DNS utilisé(s) par les *Target Hosts*.

```sh
nano dns-info.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # dns-info.yml

- hosts: all
  gather_facts: true

  tasks:
    - name: Display DNS servers
      debug:
        msg: "Serveur(s) DNS utilisé(s) : {{ ansible_facts.dns.nameservers }}"

...
```

Testons notre *playbook*.

```sh
ansible-playbook dns-info.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] *********************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Display DNS servers] *****************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Serveur(s) DNS utilisé(s) : ['10.0.2.3']"
}
ok: [debian] => {
    "msg": "Serveur(s) DNS utilisé(s) : ['4.2.2.1', '4.2.2.2', '208.67.220.220']"
}
ok: [suse] => {
    "msg": "Serveur(s) DNS utilisé(s) : ['10.0.2.3']"
}

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Place au nettoyage.

```sh
exit
vagrant destroy -f
```
