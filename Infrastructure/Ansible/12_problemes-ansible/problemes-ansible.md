# Résolution des problèmes liés à Ansible

## Résolution de problèmes concernant les playbooks

### Fichier de journalisation

Paramètre **log_path** dans la section **default** du fichier de configuration **ansible.cfg** ou à l'aide de la variable d'environnement **$ANSIBLE_LOG_PATH**

Le fichier contiendra le résultat des commandes **ansible** et **ansible-playbook**

### Module Debug

Voir la documentation du module

### Gestion des erreurs

- vérification de la syntaxe

  ```sh
  ansible-playbookplay.yml --syntax-check
  ```

- Parcourir un playbook tâche par tâche

  ```sh
  ansible-playbookplay.yml --step
  ```

- Lancer un playbook à partir d'une tâche spécifique

  ```sh
  ansible-playbookplay.yml --start-at-task="start httpd service"
  ```

### Niveau de verbosité

avec -v, -vv, -vvv, -vvvv

## Problèmes liés aux hôtes

### Utilisation du mode Check comme outil de test

La commande **ansible-playbook --check** Exécute le playbook sans modifier la configuration des hôtes gérés

Exemple :

```sh
ansible-playbook --check playbook.yml
```

:bulb: Particularités du chjeck mode dans les tâches:

- **check_mode: yes** : Toujours exécuter une tâche en check_mode, donc sans modification

  ```yml
    tasks:
    - name: task always in check mode
      shell: uname -a
      check_mode: yes
  ```

- **check_mode: yes** : Toujours exécuter une tâche normalement, même si démarrée avec  *ansible-playbook --check*

  ```yml
     tasks:
    - name: task always runs even in check mode
      shell: uname -a
      check_mode: no
  ```

:bulb: `--check --diff`: Afficher les modifications apportées aux fichiers de modèle sur les hôtes sans effectuer les modifications

```yml
ansible-playbook --check --diff playbook.yml
```

## Modules de tests

- Le module `uri`permet de vérifier si une API RESTful renvoie le contenu attendu.

  ```yml
  tasks:
    - uri:
        url: http://api.myapp.com
        return_content: yes
      register: apiresponse

    - fail:
        msg: 'version was not provided'
      when: "'version' not in apiresponse.content"
  ```

- Le module `script`prend en charge l'exécution d'un script sur des hôtes gérés et échoue si le code retourné par ce script est différent de zéro. Le script doit exister sur le nœud de contrôle et est transféré et exécuté sur les hôtes gérés.

  ```yml
    tasks:
    - script: check_free_memory
  ```

- Le module `stat` rassemble des faits pour un fichier similaire à la commande stat . Vous pouvez l'utiliser pour enregistrer une variable, puis effectuer un test pour déterminer si le fichier existe ou pour obtenir d'autres informations sur le fichier. Si le fichier n'existe pas, la tâche `stat`n'échouera pas, mais sa variable enregistrée renverra **false** pour ***.stat.exists**.

```yml
tasks:
  - name: Check if /var/run/app.lock exists
    stat:
    path: /var/run/app.lock
    register: lock

  - name: Fail if the application is running
    fail:
    when: not lock.stat.exists
```

- Utilisation du module `assert`à la place du module `fail`. Le module `assert`prend en charge une option `that` qui prend une liste de conditions. Si l'une de ces conditions est fausse, la tâche échoue. les options `success_msg`et `fail_msg`pour personnaliser le message à imprimer s'il signale une réussite ou un échec.

```yml
  tasks:
    - name: Check if /var/run/app.lock exists
      stat:
        path: /var/run/app.lock
      register: lock

    - name: Fail if the application is running
      assert:
        that:
          - not lock.stat.exists
```

## Résolution des problèmes liés aux connexions

De nombreux problèmes courants lors de l'utilisation d'Ansible pour gérer des hôtes sont associés à des connexions à l'hôte et à des problèmes de configuration liés à l'utilisateur distant et à l'augmentation des privilèges.

A vérifier :

- `remote_user` est bien configuré dans le fichier de configuration
- Clé SSH sont bien configurées ou mot de passe fourni est correct pour l'utilisateur
- **become**  est bien réglé et qu'on utilise lebon **become_user** (*root* par défaut)

## Commandes ad hoc pour les tests

- Ping de la machine

```yml
ansible demohost -m ping
```

- Ping de la machine avec augmentation des privilèges

```yml
ansible demohost -m ping --become
```

- Affichage de l'espace disque de *demohost*

```yml
ansible demohost -m command -a 'df'
```

- Affichage dela mémoire libre de *demohost*

```yml
ansible demohost -m command -a 'free -m'
```

:notebook: Documentation
[Testing Strategie](https://docs.ansible.com/ansible/2.7/reference_appendices/test_strategies.html)
[Check Mode (“Dry Run”)](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_checkmode.html)
