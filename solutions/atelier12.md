# atelier12

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-12`.

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

Nous allons écrire un playbook `chrony.yml` qui assurera la synchronisation NTP avec toutes les machines cibles.  
Le dossier `playbooks` a été créé en amont.

```sh
nano playbooks/chrony.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # chrony.yml

- hosts: redhat

  tasks:

    - name: Install chrony
      dnf:
        name: chrony
        state: present

    - name: Start & enable chronyd
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Configure chrony servers and options
      copy:
        dest: /etc/chrony.conf
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart chronyd

  handlers:

    - name: Restart chronyd
      service:
        name: chronyd
        state: restarted

...
```

Testons la syntaxe de notre playbook grâce à la commande `yamllint`.  
Si tout est bon, aucune réponse ne devrait s'afficher.

```sh
yamllint chrony.yml
```

Il est temps d'exécuter notre livre d'instructions.

```sh
ansible-playbook chrony.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [redhat] *******************************************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Install chrony] ***********************************************************************************************************************************************************************
changed: [target01]
changed: [target03]
changed: [target02]

TASK [Start & enable chronyd] ***************************************************************************************************************************************************************
changed: [target03]
changed: [target01]
changed: [target02]

TASK [Configure chrony servers and options] *************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

RUNNING HANDLER [Restart chronyd] ***********************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP **********************************************************************************************************************************************************************************
target01                   : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous allons maintenant vérifier l'idempotence de notre fichier en le réexecutant. Toutes les tâches devraient être au vert.

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
[vagrant@control playbooks]$ ansible-playbook chrony.yml

PLAY [redhat] *******************************************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Install chrony] ***********************************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Start & enable chronyd] ***************************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Configure chrony servers and options] *************************************************************************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

PLAY RECAP **********************************************************************************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

L'idempotence de notre playbook est assurée.

L'atelier est maintenant terminé. Nettoyons notre espace de travail.

```sh
exit
vagrant destroy -f
```
