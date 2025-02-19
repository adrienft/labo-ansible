# atelier17-bonus

## Exercice

### Une autre alternative est possible...

Nous allons réécrire le *playbook* `chrony-02.yml` de [l'atelier précédent](https://github.com/adrienft/labo-ansible/blob/main/solutions/atelier17.md) en y intégrant directement les variables pour chacune des distributions. Cela se matérialise par l'utilisation d'une structure `vars`. Nous n'aurons alors plus besoin de fichiers externes... Cette solution me semble plus simple et intuitive que celle consistant à utiliser des fichiers de variables externes.

```sh
nano chrony-02.yml
```

> Retrouvez ci-dessous le contenu du fichier :

```sh
---
# chrony-02.yml

- hosts: all

  vars:

    chrony:
      Debian:
        package: chrony
        service: chronyd
        confdir: /etc/chrony/chrony.conf
      Ubuntu:
        package: chrony
        service: chronyd
        confdir: /etc/chrony/chrony.conf
      Rocky:
        package: chrony
        service: chronyd
        confdir: /etc/chrony.conf
      openSUSE Leap:
        package: chrony
        service: chronyd
        confdir: /etc/chrony.conf

  tasks:

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Display the chrony configuration for the distribution
      debug:
        msg: "{{ chrony[ansible_distribution] }}"

    - name: Install chrony
      package:
        name: "{{ chrony[ansible_distribution].package }}"

    - name: Start & enable chronyd
      service:
        name: "{{ chrony[ansible_distribution].service }}"
        state: started
        enabled: true

    - name: Configure chrony servers and options
      copy:
        dest: "{{ chrony[ansible_distribution].confdir }}"
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
        name: "{{ chrony[ansible_distribution].service }}"
        state: restarted

...
```

Nous pouvons à présent tester notre livre d'instructions.

```sh
ansible-playbook chrony-02.yml
```

> Retrouvez le résultat de la commande précédente ci-dessous :

```sh
PLAY [all] *********************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [rocky]
ok: [ubuntu]
ok: [debian]
ok: [suse]

TASK [Update package information on Debian/Ubuntu] *****************************************************************************************************************************************************************************************
skipping: [rocky]
skipping: [suse]
changed: [debian]
ok: [ubuntu]

TASK [Display the chrony configuration for the distribution] *******************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": {
        "confdir": "/etc/chrony.conf",
        "package": "chrony",
        "service": "chronyd"
    }
}
ok: [debian] => {
    "msg": {
        "confdir": "/etc/chrony/chrony.conf",
        "package": "chrony",
        "service": "chronyd"
    }
}
ok: [suse] => {
    "msg": {
        "confdir": "/etc/chrony.conf",
        "package": "chrony",
        "service": "chronyd"
    }
}
ok: [ubuntu] => {
    "msg": {
        "confdir": "/etc/chrony/chrony.conf",
        "package": "chrony",
        "service": "chronyd"
    }
}

TASK [Install chrony] **********************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
ok: [rocky]
changed: [suse]

TASK [Start & enable chronyd] **************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
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
debian                     : ok=7    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=6    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
suse                       : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Tout semble fonctionner correctement.
