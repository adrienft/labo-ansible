# atelier10

## Exercice

Nous allons commencer par démarrer l'ensemble des machines virtuelles présentes dans le répertoire `atelier-10`.

```sh
vagrant up
```

Connectons-nous maintenant au contrôleur.

```sh
vagrant ssh ansible
```

L'environnement de l'atelier est préconfiguré et prêt à l'emploi.

Rendons-nous dans le répertoire du projet.

```sh
cd ansible/projets/ema/
```

Nous allons écrire un premier playbook `apache-debian.yml` qui installe Apache sur l'hôte `debian`.
Le dossier `playbooks` a été créé en amont.

```sh
nano playbooks/apache-debian-yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # apache-debian.yml

- hosts: debian

  tasks:

    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Apache on Debian Linux</title>
            </head>
            <body>
              <h1>Apache web server running on Debian Linux.</h1>
            </body>
          </html>

...
```

Testons la syntaxe de notre playbook à l'aide de ``yamllint`. Aucune réponse équivaut à aucun problème.

```sh
yamllint apache-debian.yml
```

L'heure est arrivée... il est temps d'exécuter notre livre d'instructions.

```sh
ansible-playbook apache-debian-yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [debian] *************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
ok: [debian]

TASK [Update package information] *****************************************************************************************************************************************
changed: [debian]

TASK [Install Apache] *****************************************************************************************************************************************************
changed: [debian]

TASK [Start & enable Apache] **********************************************************************************************************************************************
ok: [debian]

TASK [Install custom web page] ********************************************************************************************************************************************
changed: [debian]

PLAY RECAP ****************************************************************************************************************************************************************
debian                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous pouvons afficher le contenu de la page grâce à la commande `curl debian`.

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
[vagrant@ansible playbooks]$ curl debian
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Apache on Debian Linux</title>
  </head>
  <body>
    <h1>Apache web server running on Debian Linux.</h1>
  </body>
</html>
```

Écrivons un second playbook nommé `apache-rocky.yml` qui installe Apache sur l'hôte `rocky`.

```sh
nano playbooks/apache-rocky.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # apache-rocky.yml

- hosts: rocky

  tasks:

    - name: Update package information
      dnf:
        name: '*'
        state: latest
        update_cache: true

    - name: Install Apache
      dnf:
        name: httpd
        state: present

    - name: Start & enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Apache on Rocky Linux</title>
            </head>
            <body>
              <h1>Apache web server running on Rocky Linux.</h1>
            </body>
          </html>

...
```

Testons la syntaxe du playbook `yamllint apache-rocky.yml`. Aucune réponse équivaut à aucun problème.

Il est temps d'exécuter notre livre d'instructions.

```sh
ansible-playbook apache-rocky.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [rocky] **************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
ok: [rocky]

TASK [Update package information] *****************************************************************************************************************************************
ok: [rocky]

TASK [Install Apache] *****************************************************************************************************************************************************
changed: [rocky]

TASK [Start & enable Apache] **********************************************************************************************************************************************
changed: [rocky]

TASK [Install custom web page] ********************************************************************************************************************************************
changed: [rocky]

PLAY RECAP ****************************************************************************************************************************************************************
rocky                      : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous pouvons à présent afficher la page web par défaut de l'hôte `rocky`.

```sh
curl rocky
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
[vagrant@ansible playbooks]$ curl rocky
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Apache on Rocky Linux</title>
  </head>
  <body>
    <h1>Apache web server running on Rocky Linux.</h1>
  </body>
</html>

```

Nous allons maintenant écrire un dernier playbook `apache-suse.yml` qui installe Apache sur l'hôte `suse`.

```sh
nano playbooks/apache-suse-yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---  # apache-suse.yml

- hosts: suse

  tasks:

    - name: Install Apache
      zypper:
        name: apache2
        state: present

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Apache on SUSE Linux</title>
            </head>
            <body>
              <h1>Apache web server running on SUSE Linux.</h1>
            </body>
          </html>

...
```

Testons la syntaxe de notre nouveau playbook grâce à la commande `yamllint apache-suse.yml`. Si tout est bon, aucune réponse n'est attendue.

Exécutons notre livre d'instructions.

```sh
ansible-playbook apache-suse.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [suse] ***************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
ok: [suse]

TASK [Install Apache] *****************************************************************************************************************************************************
changed: [suse]

TASK [Start & enable Apache] **********************************************************************************************************************************************
changed: [suse]

TASK [Install custom web page] ********************************************************************************************************************************************
changed: [suse]

PLAY RECAP ****************************************************************************************************************************************************************
suse                       : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Affichons à présent le contenu de la page.

```sh
curl suse
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Apache on SUSE Linux</title>
  </head>
  <body>
    <h1>Apache web server running on SUSE Linux.</h1>
  </body>
</html>
```

L'atelier touche à sa fin. Place au nettoyage.

```sh
exit
vagrant destroy -f
```
