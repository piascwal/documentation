# Configuration d'un parallélisme

- Gestion du nombre de connexion simultanées ouvertes par Ansible sur les hôtes.
- Déterminer comment Ansible traite les groupes d'hôtes via les tâches du play.

## Configuration via les forks

Utiliser la directive `forks` dans le fichier **ansible.cfg** pour déterminer le nombre maximal de connexion simultanées:

```txt
forks = 5
```

:bulb: Si les hôtes sons sous Linux, la plus part des tâches s'exécutent sur les hôtes. Ainsi le noeud de contrôle est moins chargé et la valeur du fork peut être beaucoup plus élevée (aux alentours de 100).

:bulb: En ligne de commande l'option `-f` et `--forks` permet de spécifier le nombre de forks à utiliser.

## Gestion des mises à jour progressive

Utilisation du mot clé `serial` permet te traiter les hôtes par lot, mais à la différence de **forks** l'intégralité du play est exécuté avant de passer au lot suivant :

Ceci peut être utile lors de la mise à jour de serveur web derrière un loadbalancer, afin que tous les serveurs ne se retrouvent pas hors service au même moment.

Example :

```yml
---
- name: Lancement de la mise à jour
  hosts: webservers
  serial: 2
  tasks:
  - name: Derniers packages apache httpd installés
    yum:
      name: httpd
      state: latest
    notify: restart apache

  handlers:
  - name: restart apache
    service:
      name: httpd
      state: restarted
```

:notebook: Documentation

[Mises à jour progressives](https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html#rolling-update-batch-size)
[Optimisation des performances](https://www.ansible.com/blog/ansible-performance-tuning)
