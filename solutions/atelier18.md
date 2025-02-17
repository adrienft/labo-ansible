# atelier18

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-18`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur (ou *Control Host*).

```sh
vagrant ssh ansible
```

L'environnement de l'atelier est préconfiguré et prêt à l'emploi.

Rendons-nous dans le répertoire *playbooks* du projet.

```sh
cd ansible/projets/ema/playbooks/
```

Nous allons créer un nouveau *playbook* `chrony.yml` qui déploiera un fichier de configuration personnalisé sur nos cibles. Pour cela, reprenons le travail effectué lors du [dernier atelier](https://github.com/adrienft/labo-ansible/blob/main/solutions/atelier17.md) et apportons les modifications nécessaires à la structure de l'ancien *playbook*.

```sh
nano chrony.yml
```

> Retrouvez ci-dessous le contenu du nouveau fichier :

```sh
---  # chrony.yml

- hosts: all

  tasks:

    - name: Load distribution-specific parameters
      include_vars: >
        chrony_{{ansible_distribution|lower|replace(" ", "-") }}.yml

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install chrony
      package:
        name: "{{chrony_package}}"

    - name: Start & enable chronyd
      service:
        name: "{{chrony_service}}"
        state: started
        enabled: true

    - name: Configure chrony servers and options
      template:
        dest: "{{chrony_confdir}}"
        mode: 0644
        src: chrony.conf.j2
      notify: Restart chronyd

  handlers:

    - name: Restart chronyd
      service:
        name: "{{chrony_service}}"
        state: restarted

...
```

Assurez-vous d'avoir recréé le dossier `vars` et les fichiers de variables correspondants aux différentes distributions supportées.

Nous allons maintenant créer un répertoire nommé `templates` et y ajouter un fichier `chrony.conf.j2`. La première ligne de ce fichier devra contenir le chemin complet vers le fichier de configuration de `chrony`.

```sh
mkdir templates
nano templates/chrony.conf.j2
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
# {{chrony_confdir}}
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

La variable `{{chrony_confdir}}` contient le chemin vers le fichier de configuration du service. Cette variable est définie spécifiquement pour chaque distribution.

Nous pouvons à présent tester notre *playbook*.

```sh
ansible-playbook chrony.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] *********************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]
ok: [ubuntu]

TASK [Load distribution-specific parameters] ***********************************************************************************************************************************************************************************************
ok: [rocky]
ok: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Update package information on Debian/Ubuntu] *****************************************************************************************************************************************************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Install chrony] **********************************************************************************************************************************************************************************************************************
ok: [rocky]
changed: [debian]
changed: [ubuntu]
changed: [suse]

TASK [Start & enable chronyd] **************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [ubuntu]
changed: [suse]

TASK [Configure chrony servers and options] ************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
changed: [suse]
changed: [rocky]

RUNNING HANDLER [Restart chronyd] **********************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
changed: [rocky]
changed: [suse]

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
debian                     : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=6    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
suse                       : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Connectons-nous à l'hôte `suse` pour vérifier le bon fonctionnement de notre configuration.

```sh
ssh suse
cat /etc/chrony.conf
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
vagrant@suse:~> cat /etc/chrony.conf
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

Vérifions la bonne prise en compte de notre configuration par `chrony`.

```sh
chronyc sourcestats
```

```sh
vagrant@suse:~> chronyc sourcestats
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
time.cloudflare.com        18  11   912     +0.076     11.481  +2321ns  2493us
82-65-235-151.subs.proxa>   6   3   324     -8.837     32.281  -3385us  1167us
ntp.tuxfamily.net          10   5   647     +5.482      3.354   +666us   393us
dalaran.sceen.net          17   8   913    +10.183     11.558  -2351us  3534us
```

Tout semble fonctionner correctement. Nous retrouvons bien nos quatre serveurs de temps.

Tous les ateliers sont désormais terminés 🚀

```sh
exit
vagrant destroy -f
```
