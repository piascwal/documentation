# Rôles

**Objectif**: Réutilisation de code.

Exemple : Un play qui configure une base de données MySQL pour une application pourrait être réaffecté, avec des noms d'hôte, des mots de passe et des utilisateurs différents, pour configurer une base de données MySQL pour une autre application.

Les rôles :

- Groupe du contenu et faciliter le partage de code
- Peuvent être développé en parallèle par plusieurs administrateurs
- Définition des élément essentiels d'un type de système( ex: serveur web, serveur bd)
- Facilite la gestion de projets volumineux

:bulb: Les rôles peuvent provenir d'autres sources. Le package `rhel-system-roles` de RHEL inclus certains rôles.

## Structure

- Utilisation d'une structure normalisée de sous-répertoires et de fichiers
- Le répertoire de niveau supérieur définit le nom du rôle
- Les sous répertoires sont nommés en fonctions de leurs objectifs (taskks, handlers)
- Les sous-répertoiresfilesettemplatescontiennent des fichiers auxquels les tâches d'autres fichiers YAML font référence

Exemple de structure (Tous les rôles n'auront pas tous ces répertoires):

```txt
 user.example/
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```

## Variables et valeurs par défaut

- Les **variables de rôle** sont définies dans le répertoire `vars/main.yml` contenant des paires clé:valeur. Ces variables ont une **priorité élevée** (impossible de les remplacer par des variables d'inventaire)

- Les **variables par défaut** sont définies dans le répertoire `defaults/main.yml`. Elles ont la **priorité la plus faible** (peuvent être remplacé par des variable d'inventaire par exemple).

- Définir une variable spécifique dans **vars/main.yml** ou **defaults/main.yml**, mais pas simultanément

- Utiliser des variables par défaut lorsque les valeurs correspondantes doivent être remplacées

## Utilisation de rôle dans un playbook

```yml
---
- hosts: remote.example.com
  roles:
    - role1
    - role2
```

:bulb: Lors de l'utilisation d'une section *roles* pour importer des rôles dans un play, les rôles sont exécutés en premier, avant toutes les tâches que l'on définit pour ce play.

Exemple : Role 1 utilise les variables par défaut et rôle 2 utilise les variables fournies

```yml
---
- hosts: remote.example.com
  roles:
    - role: role1
    - role: role2
      var1: val1
      var2: val2
```

## Contrôle de l'ordre d'execution

- Les tâches s'exécutent dans l'ordre indiqué dans la liste. Les gestionnaires notifiés sont ensuite exécutés.
- Si un rôle est présent, les tâches de rôle sont ajoutées au début de la liste des tâches.
- Si un second rôle est ajouté, sa liste de tâche est ajoutée après le premier rôle
- Les gestionnaires de rôles sont ajoutés de la même manière que les tâches de rôle. Ainsi les gestionnaires de rôles sont d'abord ajoutés à la liste des gestionnaires, suivis par ceux définis dans la section *handler* du play
- La directive `pre_tasks` permet d'exécuter des tâches de play avant les rôles. Si cette tâche notifie un gestionnaire, les tâches de ce gestionnaire sont exécutées avant les rôles ou tâches normales
- La directive `post_task` exécutent les tâches après que les tâches normales du play et les gestionnaires notifiés aient été exécutés.

Exemple :

```yml
- name: Illustration de l'ordre d'execution
  hosts: remote.example.com
  pre_tasks:
    - debug:
        msg: 'pre-task'
      notify: my handler
  roles:
    - role1
  tasks:
    - debug:
        msg: 'first task'
      notify: my handler
  post_tasks:
    - debug:
        msg: 'post-task'
      notify: my handler
  handlers:
    - name: my handler
      debug:
        msg: Running my handler
```

## Inclusion / Import de rôle dans les tâches

Directives :

- `include_role`: Import dynamique d'un rôle
- `import_role` : Import statique d'un rôle

Exemple:

```yml
- name: Exécution d'un rôle en tant que tâche
  hosts: remote.example.com
  tasks:
    - name: Tâche normale
      debug:
        msg: 'première tâche'
    - name: Tâche incluant role2
      include_role: role2
```

:notebook: Documentation
[Roles](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_reuse_roles.html)
