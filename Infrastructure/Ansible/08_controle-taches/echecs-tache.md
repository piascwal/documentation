# Gestion des échecs de tâche

- Ansible évalue les codes retour de chaque tâche.
- En générérale si une tâche échoue, Ansible met immédiatement fin au reste du play sur cet hôte et ignore les tâches suivantes.

## Ignorer l'échec d'une tâche

Le mot clé `ignore_errors` avec la valeur `yes` dans une tâche afin de poursuivre l'exécution du playbook sur l'hôte même en cas d'erreur.

Exemple :

```yml
- name: Latest version of notapkg is installed
  yum:
    name: notapkg
    state: latest
  ignore_errors: yes
```

## Execution d'un gestionnaire en cas d'échec

- Par défaut lorsqu'une tâche échoue, tout gestionnaire qui a été notifié par des tâches précédentes du pmay n'est pas executé.

L'utilisation du mot clé `force_handlers` avec la valeur `yes` sur le play, permet aux gestionnaires notifiés d'être quand même appelés.

Exemple:

```yml
---
- hosts: all
  force_handlers: yes
  tasks:
    - name: a task which always notifies its handler
      command: /bin/true
      notify: restart the database

    - name: a task which fails because the package doesn't exist
      yum:
        name: notapkg
        state: latest

  handlers:
    - name: restart the database
      service:
        name: mariadb
          state: restarted
```

## Spécification des conditions d'échec d'une tâche

Le mot clé `failed_when` sur une tâche permet de spécifier les conditions indiquant que la tâche a echoué.

Ceci est souvent utilisé avec des modules de commande pouvant exécuter une commande avec succès, mais le résultat de la commande indique un échec.

Exemple :

```yml
tasks:
  - name: Run user creation script
    shell: /usr/local/bin/create_users.sh
    register: command_result
    failed_when: "'Password missing' in command_result.stdout"
```

Le module `fail` peut être utilisé pour forcer l'échec de tâche.

:warning: Le statut sera surcharger mais la tâche sera exécuter quand même

Exemple : réécriture de l'éxemple ci-dessus:

```yml
tasks:
  - name: Run user creation script
    shell: /usr/local/bin/create_users.sh
    register: command_result
    ignore_errors: yes

  - name: Report script failure
    fail:
      msg: "The password is missing in the output"
    when: "'Password missing' in command_result.stdout"
```

## Surcharger l'état "changed" d'une tâche

**Rappel**: Une tâche effectuant une modification à un hôte signale l'état `changed`et notifie les gestionnaires.

Le mot clé `changed_when`, permet de contrôler les cas où une tâche signale une modification.

Exemple : le module `shell`en charge d'obtenir les information d'identification Kerberos, pour les tâches ultérieurs, signal toujours un état `changed` lorsqu'il est exécuté. Pour supprimer cette modification `changed_when: false` est défini pour signifier un état `ok`ou `failed`. Voir ci-dessous.

Toujours renvoyer : OK

```yml
  - name: get Kerberos credentials as "admin"
    shell: echo "{{ krb_admin_pass }}" | kinit -f admin
    changed_when: false
```

Exemple2: **conditionner le statut renvoyé**. Ici en fonction d'une variable enregistré:

```yml
tasks:
  - shell:
      cmd: /usr/local/bin/upgrade-database
    register: command_result
    changed_when: "'Success' in command_result.stdout"
    notify:
      - restart_database

handlers:
  - name: restart_database
     service:
       name: mariadb
       state: restarted
```

## Bloc Ansible et Gestion des erreurs

- Les blocs sont des clauses qui regroupent des tâches
- Les blocs peuvent être utilisés pour contrôler la façon dont les tâches sont exécutées.

Exemple: Utilisation de `when` pour appliquer plusieurs tâches conditionnelles:

```yml
- name: block example
  hosts: all
  tasks:
    - block:
      - name: package needed by yum
        yum:
          name: yum-plugin-versionlock
          state: present
      - name: lock version of tzdata
        lineinfile:
          dest: /etc/yum/pluginconf.d/versionlock.list
          line: tzdata-2016j-1
          state: present
      when: ansible_distribution == "RedHat"
```

### Gestions des erreurs avec les blocs

Les instructions `rescue`et `always` permettent la gestion des erreurs des blocs.

- **block**: définit les tâches principales à exécuter.

- **rescue**: définit les tâches devant s'exécuter si les tâches définies dans la clause *block* échouent.

- **always**: définit les tâches qui seront systématiquement exécutées, indépendamment de la réussite ou de l'échec des tâches définies dans les clauses *block* et *rescue*.

Exemple :

```yml
tasks:
    - block:
        - name: upgrade the database
          shell:
            cmd: /usr/local/lib/upgrade-database
      rescue:
        - name: revert the database upgrade
          shell:
            cmd: /usr/local/lib/revert-database
      always:
        - name: always restart the database
          service:
            name: mariadb
            state: restarted
```

:bulb: La conditionwhensur une clause `block` s'applique également à ses clauses `rescue` et `always` le cas échéant.
