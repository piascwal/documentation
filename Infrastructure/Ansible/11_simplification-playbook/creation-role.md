# Création de rôles

Etapes :

- Créer la structure du répertoire de rôles
- Définir le contenu du rôle
- Utiliser le rôle dans un playbook

:bulb: Répertoires des rôles

- Par défaut, Ansible recherche les rôles dans un sous-répertoire appelé *roles* dans le répertoire contenant le playbook Ansible.

- Si Ansible ne trouve pas le rôle à l'endroit par défaut, il examine les répertoires spécifiés par le paramètre de configuration `roles_path`, dans l'ordre. Cette variable contient une liste de répertoires à rechercher, séparés par le signe deux-points. La valeur par défaut de cette variable est:

  ```txt
  ~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
  ```

  Ce qui permet d'installer des rôles partagés par plusieurs projets.

Exemple de structure :

```txt
roles/
└── motd
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    └── templates
        └── motd.j2
```

## Création d'un squelette de rôle

- L'outil `ansible-galaxy` permet de gérer les rôles Ansible.
- La commande `ansible-galaxy init my_new_role` créé la structure de répertoire pour le rôle *my_new_role*

## Définition du contenu d'un rôle

Bonnes pratiques :

- Chaque rôle a son propre référentiel de contrôle de version (git)
- Les informations sensibles (mdp, clé ssh), ne doivent pas être stockée dans le référentiel de rôle.
- Utiliser *ansible-galaxy init* pour démarrer un rôle, puis supprimer les répertoire dont on a pas besoin
- Créer et maintenir le *README.md* et les fichiers *meta/main.yml*
- Un rôle a **une** fonction spécifique
- Réutiliser et refactorisez les rôles souvent

## Dépendances d'un rôle

Objectif : Inclure dans un rôle d'autre rôle en tant que dépendance

Les dépendances sont définies dans le fichier `meta/main.yml`

Exemple de fichier `meta/main.yml`:

```yml
---
dependencies:
  - role: apache
    port: 8080
  - role: postgres
    dbname: serverlist
    admin_user: felix
```

:bulb: Par défaut, les rôles ne peuvent être ajoutés en tant que dépendances à un playbook qu'une seule fois. Un rôle qui serait listé comme dépendance d'un autre rôle ne serait pas exécuté. Ce comportement peut être modifié en définissant la variable **allow_duplicates** sur **yes** dans le fichier **meta/main.yml**.

## Utilisation du rôle dans un playbook

Utiliser la directive `roles`

```yml
---
- name: utilisation de mon-role dans le playbook
  hosts: remote.example.com
  user: devops
  become: true
  roles:
    - mon-role
```
