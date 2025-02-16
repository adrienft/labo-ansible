# atelier15

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-15`.

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

Nous allons écrire un *playbook* `kernel.yml` qui affichera les informations détaillées du noyau de toutes nos machines cibles. Pour cela, nous utiliserons dans un premier temps le module `debug` et le paramètre `msg`.

```sh
nano kernel.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:

    - name: Display kernel detailed info
      command: uname -a
      changed_when: false
      register: df_cmd

    - debug:
        msg: "{{df_cmd.stdout_lines}}"

...
```

Il est l'heure de tester notre livre d'instructions.

```sh
ansible-playbook kernel.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] **********************************************************************************************************************************************************************************

TASK [Display kernel detailed info] *********************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [debug] ********************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": [
        "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
ok: [debian] => {
    "msg": [
        "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
    ]
}
ok: [suse] => {
    "msg": [
        "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
    ]
}

PLAY RECAP **********************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons maintenant essayer avec le paramètre `var` du module `debug`.

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:

    - name: Display kernel detailed info
      command: uname -a
      changed_when: false
      register: df_cmd

    - debug:
        var: df_cmd.stdout_lines

...
```

```sh
ansible-playbook kernel.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] **********************************************************************************************************************************************************************************

TASK [Display kernel detailed info] *********************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [debug] ********************************************************************************************************************************************************************************
ok: [rocky] => {
    "df_cmd.stdout_lines": [
        "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
ok: [debian] => {
    "df_cmd.stdout_lines": [
        "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
    ]
}
ok: [suse] => {
    "df_cmd.stdout_lines": [
        "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
    ]
}

PLAY RECAP **********************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Enfin, nous allons terminer cet exercice en écrivant un *playbook* `packages.yml` capable d'afficher le nombre total de paquets RPM installés sur les hôtes `rocky` et `suse`.

```sh
nano packages.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # packages.yml

- hosts: rocky, suse
  gather_facts: false

  tasks:

    - name: Get number of installed RPM packages
      shell: rpm -qa | wc -l
      changed_when: false
      register: df_cmd

    - debug:
        var: df_cmd.stdout_lines

...
```
Notez que nous faisons désormais appel au module `shell` en raison de l'utilisation d'un *pipe* (ou tube en français - `|`).

Testons notre nouveau fichier `.yml`.

```sh
ansible-playbook packages.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [rocky, suse] **************************************************************************************************************************************************************************

TASK [Get number of installed RPM packages] *************************************************************************************************************************************************
ok: [rocky]
ok: [suse]

TASK [debug] ********************************************************************************************************************************************************************************
ok: [rocky] => {
    "df_cmd.stdout_lines": [
        "671"
    ]
}
ok: [suse] => {
    "df_cmd.stdout_lines": [
        "917"
    ]
}

PLAY RECAP **********************************************************************************************************************************************************************************
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

L'atelier est maintenant terminé. Nettoyons notre espace de travail.

```sh
exit
vagrant destroy -f
```
