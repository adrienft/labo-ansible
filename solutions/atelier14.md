# atelier14

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-14`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh
vagrant ssh control
```

L'environnement de l'atelier est préconfiguré et prêt à l'emploi.

Rendons-nous dans le répertoire du projet.

```sh
cd ansible/projets/ema/
```

Nous allons écrire un premier *playbook* `myvars1.yml` qui affichera nos véhicules préférés en utilisant deux variables définies en tant que *play vars*.
Le dossier `playbooks` a été créé en amont.

```sh
nano playbooks/myvars1.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # myvars1.yml

- hosts: localhost
  gather_facts: false

  vars:
    mycar: Bentley Continental GT Mulliner
    mybike: Harley Davidson Breakout

  tasks:
    - debug:
        msg: "Voiture préférée : {{mycar}}, Moto préférée : {{mybike}}"

...
```

Il est temps de tester notre livre d'instructions.

```sh
ansible-playbook myvars1.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Bentley Continental GT Mulliner, Moto préférée : Harley Davidson Breakout"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons à présent remplacer nos variables, l'une et l'autre puis les deux à la fois, en utilisant les *extra vars*.  
Commençons par la voiture de nos rêves.

```sh
ansible-playbook myvars1.yml -e mycar=Alpine
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Alpine, Moto préférée : Harley Davidson Breakout"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Puis passons à la moto...

```sh
ansible-playbook myvars1.yml -e mybike=Honda
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Bentley Continental GT Mulliner, Moto préférée : Honda"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Modifions maintenant les deux variables.

```sh
ansible-playbook myvars1.yml -e mycar=Alpine -e mybike=Honda
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Alpine, Moto préférée : Honda"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Écrivons un second *playbook* nommé `myvars2.yml` faisant la même chose que `myvars1.yml`, mais en utilisant `set_fact` pour les deux variables.

```sh
nano playbooks/myvars2.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # myvars2.yml

- hosts: localhost
  gather_facts: false

  tasks:

    - name: Define variables
      set_fact:
        mycar: Bentley Continental GT Mulliner
        mybike: Harley Davidson Breakout

    - debug:
        msg: "Voiture préférée : {{mycar}}, Moto préférée : {{mybike}}"

...
```

Testons notre livre d'instructions.

```sh
ansible-playbook myvars2.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [Define variables] *********************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Bentley Continental GT Mulliner, Moto préférée : Harley Davidson Breakout"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Une fois n'est pas coutume, nous allons maintenant remplacer nos variables en utilisant les *extra vars*.

```sh
ansible-playbook myvars2.yml -e mycar=Alpine -e mybike=Honda
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [Define variables] *********************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Voiture préférée : Alpine, Moto préférée : Honda"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Écrivons un nouveau *playbook* qui affichera le contenu des variables `mycar` et `mybike` définies dans le répertoire `group_vars`.

```sh
nano playbooks/myvars3.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # myvars3.yml

- hosts: all
  gather_facts: false

  tasks:
    - debug:
        msg: "Voiture préférée : {{mycar}}, Moto préférée : {{mybike}}"

...
```

Créons maintenant le répertoire `group_vars`.

```sh
mkdir ~/ansible/projets/ema/group_vars
```

Une fois le dossier disponible, nous pouvons créer un fichier `all.yml` dans ce dernier.

```sh
nano ~/ansible/projets/ema/group_vars/all.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # group_vars/all.yml

mycar: VW
mybike: BMW

...
```

Testons à présent notre nouveau `playbook` (depuis le répertoire `playbooks`).  
Les valeurs `VW` et `BMW` sont définies comme valeurs par défaut pour tous les hôtes.

```sh
ansible-playbook myvars3.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] **********************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [target02] => {
    "msg": "Voiture préférée : VW, Moto préférée : BMW"
}
ok: [target01] => {
    "msg": "Voiture préférée : VW, Moto préférée : BMW"
}
ok: [target03] => {
    "msg": "Voiture préférée : VW, Moto préférée : BMW"
}

PLAY RECAP **********************************************************************************************************************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons maintenant faire le nécéssaire pour remplacer les valeurs `VW` et `BMW` par `Mercedes` et `Honda` sur l’hôte `target02`.  
Pour ce faire, nous devons créer un fichier `target02.yml` dans un nouveau répertoire nommé `host_vars`.

```sh
mkdir ~/ansible/projets/ema/host_vars
nano ~/ansible/projets/ema/host_vars/target02.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # host_vars/target02.yml

mycar: Mercedes
mybike: Honda

...
```

Testons notre livre d'instructions.

```sh
ansible-playbook myvars3.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] **********************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Voiture préférée : VW, Moto préférée : BMW"
}
ok: [target02] => {
    "msg": "Voiture préférée : Mercedes, Moto préférée : Honda"
}
ok: [target03] => {
    "msg": "Voiture préférée : VW, Moto préférée : BMW"
}

PLAY RECAP **********************************************************************************************************************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Terminons cet atelier en écrivant un *playbook* qui affichera le nom et le mot de passe d'un utilisateur à l'aide des variables `user` et `password`. Ces variables seront saisies de manière interactive pendant l'exécution du fichier. Nous veillerions également à ce que le mot de passe ne s'affiche pas en clair pendant la saisie.

```sh
nano playbooks/display_user.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # display_user.yml

- hosts: localhost
  gather_facts: false

  vars_prompt:

    - name: user
      prompt: Veuillez préciser un nom d'utilisateur
      default: microlinux
      private: false

    - name: password
      prompt: Veuillez préciser un mot de passe pour l'utilisateur
      default: yatahongaga
      private: true

  tasks:
    - debug:
        msg: "L'utilisateur {{user}} a pour mot de passe : {{password}}"

...
```

Testons à présent notre dernier *playbook*.

```sh
ansible-playbook display_user.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
Veuillez préciser un nom d'utilisateur [microlinux]: Adrien
Veuillez préciser un mot de passe pour l'utilisateur [yatahongaga]:

PLAY [localhost] ****************************************************************************************************************************************************************************

TASK [debug] ********************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "L'utilisateur Adrien a pour mot de passe : bonjour!2"
}

PLAY RECAP **********************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

L'atelier touche à sa fin. Place au nettoyage.

```sh
exit
vagrant destroy -f
```
