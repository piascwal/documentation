# Faits Ansible

Ce sont des variables automatiquement détectées par Ansible sur un hôte géré.
Les faits contiennent des informations prores à l'hôte qui peuvent être utilisées comme des variables dans les plays, conditions, boucles.

Exemple :

- le nom de l'hôte
- la version du noyau
- les interfaces réseau
- les adresses IP
- la version du système d'exploitation
- différentes variables d'environnement
- le nombre d'UC
- la mémoire disponible
- l'espace disque disponible

## La tâche Gathering Facts

La tâche `Gathering Facts` exécuté en début de playbook a pour rôle d'executer automatiquement le module `setup`afin de rassembles les `faits`.

## Afficher les faits des hôtes gérés

Exemple de playbook:

```yml
- name: Fact dump
  hosts: all
  tasks:
    - name: Print all facts
      debug:
        var: ansible_facts
```

Exemple 2 :

```yml
---
- hosts: all
  tasks:
  - name: Prints various Ansible facts
    debug:
      msg: >
        L'adresse IPv4 de {{ ansible_facts.fqdn }}
        est {{ ansible_facts.default_ipv4.address }}
```

exemple de faits :

| Fait | Variable |
| ------ | ------ |
| Nom d'hôte abrégé |ansible_facts['hostname'] |
| Nom de domaine complet |ansible_facts['fqdn'] |
| Adresse IPv4 principale (en fonction du routage) | ansible_facts['default_ipv4']['address'] |
| Liste des noms de toutes les interfaces réseau |ansible_facts['interfaces'] |
|Taille de la partition de disque /dev/vda1 |ansible_facts['devices']['vda']['partitions']['vda1']['size'] |
| Liste des serveurs DNS |ansible_facts['dns']['nameservers'] |
| Version du noyau en cours d'exécution | ansible_facts['kernel']

## Désactiver les fait sur un play

utiliser l'instruction suivante `gather_facts: no`:

```yml
---
- name: This play gathers no facts automatically
  hosts: large_farm
  gather_facts: no
```

:bulb: le module `setup` peut être utilisé pour manuellement collecter les faits même s'ils ont été désactivés

## Faits personnalisés

- Ils sont stocker localement sur chaque hôte
- Il sont inclus à la liste des faits standards
- Ils sont collectés par le module setup
- Ils sont définis dans un fichier statique
- Ils sont au format INI ou JSON
- Ils peut sagir de scripts exécutable générant du JSON

Par défaut le module setup charge les les faits personnalisés depuis les fichiers de scripts du dossier `/etc/ansible/facts.d`de chaque hôte.
Le nom de chaque fichier ou script doit se terminer par `.fact`

Exemple format INI:

```ini
[packages]
web_package = httpd
db_package = mariadb-server

[users]
user1 = joe
user2 = jane
```

Exemple format JSON

```json
{
  "packages": {
    "web_package": "httpd",
    "db_package": "mariadb-server"
  },
  "users": {
    "user1": "joe",
    "user2": "jane"
  }
}
```

:bulb: Les faits personnalisés sont stockés par le module `setup`dans la variable `ansible_facts.ansible_local`

Exemple
si le fait se trouve dans le répertoire `/etc/ansible/facts.d/custom.facts`.
La valeur de `ansible_facts.ansible_local['custom']['users']['user1']` est égale à `joe`

## Variables magiques

Ce ne sont pas des faits, mais sont configurées automatiquement par Ansible.

Les plus communes :

- **hostvars** : Contient les variables pour les hôtes gérés et peut être utilisée afin d'obtenir les valeurs des variables d'un autre hôte géré. Cela n'inclut pas les faits de l'hôte géré s'ils n'ont pas encore été collectés pour cet hôte

- **group_names** : Liste de tous les groupes dans lesquels se trouve l'hôte géré actuel.

- **groups** : Liste de tous les groupes et hôtes de l'inventaire.

- **inventory_hostname** : Contient le nom d'hôte de l'hôte géré actuel tel que configuré dans l'inventaire. Il peut, pour différentes raisons, être différent du nom d'hôte signalé par les faits.

Pour les autres variables magiques  consulter ce [lien](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable).
