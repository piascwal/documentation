# Commande ad hoc

Permet de lancer une **commande simple**, que l'on a pas besoin de sauvegarder pour l'executer plus tard, sans nécessité d'écrire un playbook.

Exemple de cas d'utilisation:

- Vérifier qu'une ligne existe dans /etc/hosts
- redémarrer un service
- vérifier qu'un package logiciel est à jour

## Passer des arguments

Les arguments sont passés via l'option `-a`

**Exemple1** : vérifier que l'utilisateur toto a bien l'UID 4000, grâce au module *user*

```sh
ansible -m user -a 'name=toto uid=4000 state=present' servera.lab.example.com
```

**Exemple2** : executer la commande `hostname`sur le group my-hosts

```sh
ansible my-hosts -m command -a '/usr/bin/hostname'
```