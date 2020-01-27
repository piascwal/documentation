# Inclusion et importation de fichiers

- **Inclure** du contenu est une opération **dynamique** lors de l'exécution du playbook à mesure que le contenu est atteint
- **Importer** du contenu est une opération **statique**.Ansible pré-traite le contenu importé lors de l'analyse initiale du playbook, avant le début de l'exécution.

## Importation de playbook

Utilisation de la directive `import_playbook`. Permet d'avoir un playbook maitre qui importe des playbooks en lui.

:bulb: Remarques

- Le directive est utilisable qu'à haut niveau du playbook et n'est pas utilisable dans un play.
- Les playbooks sont importés dans l'ordre d'écriture.

Exemple:

```yml
- name: Préparation du serveur web
  import_playbook: web.yml

- name: Préparation du serveur de BD
  import_playbook: db.yml
```

## Importation de tâches

Utilisation de la directive `import_tasks`. Permet d'importer statiquement un fichier de tâche dans un play.

:bulb: Remarques

- Lors de l'utilisation de cette directive des instructions conditionneles (ex: when) définies lors de l'importation sont appliquées à chacune des tâches importées.
- Les boucles ne peuvent pas être utilisées avec cette directive
- Il n'est pas possible d'utiliser une variable d'inventaire d'hôte ou de groupe pour spécifier le nom du fichier à importer
- La commande **ansible-playbook --list-tasks** n'affiche pas les tâches incluses
- La commande **ansible-playbook --start-at-task** ne peut pas référencer une tâche incluse
- L'instruction **notify** ne peut pas être utilisée pour déclencher un gestionnaire figurant dans un fichier de tâche inclus.

Exemple:

```yml
---
- name: Installation du serveur web
  hosts: webservers
  tasks:
  - import_tasks: webserver_tasks.yml
```

## Variables pour les play et tâches externes

Exemple:

- task.yml :

```yml
---
  - name: Install the {{ package }} package
    yum:
      name: "{{ package }}"
      state: latest
  - name: Start the {{ service }} service
    service:
      name: "{{ service }}"
      enabled: true
      state: started
```

- play.yml :

```yml
  tasks:
    - name: Import task file and set variables
      import_tasks: task.yml
      vars:
        package: httpd
        service: service
```

:notebook: Documentation

[Including and Importing](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_reuse_includes.html)
[Creating Reusable Playbooks](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_reuse.html)
[Conditionals](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html#id5)
