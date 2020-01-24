# Playbook

- Un playbook commence par `---` et peut se terminer par ```...```

- Un plyabook est défini comme une liste de play commençant par un tiret et un espace:

  ```yml
  - play1
  - play2
  - play3
  ```

- Exemple de playbook simple:

    ```yml
    ---
    # Nom du play
    - name: Exemple de play
    # Host sur lesquels sera executé le play
    hosts: webservers
    tasks:
      # Nom de la tâche
      - name: Première tâche
        #module à utiliser
        user:
          #paramètre du module
          name: myuser
          uid: 4000
          state: present

      - name: Tâche suivante
        service:
          name: httpd
          enabled: true
    ...
    ```

- Exemple avec surcharge des valeurs de configuration

```yml
- name: /etc/hosts is up to date
  hosts: datacenter-west
  remote_user: automation
  become: yes

  tasks:
    - name: server.example.com in /etc/hosts
      lineinfile:
        path: /etc/hosts
        line: '192.0.2.42 server.example.com server'
        state: present
```

## Execution de playbook

La commande à utilisé est la suivante :

```sh
ansible-playbook site.yml
```

### Augmentation du niveau de verbosité

Il existe 4 niveaux de verbosité pouvant être appliqué via les options suivantes:

```sh

# Les résultats de la tâche sont affichés.
-v

# Les résultats et la configuration de la tâche sont affichés.
-vv

# Inclut des informations sur les connexions aux hôtes gérés.
-vvv

# Ajoute des options de détails supplémentaires aux plug-ins de connexion, notamment les utilisateurs en cours actifs dans les hôtes gérés pour exécuter des scripts, et les scripts exécutés.
-vvvv
```

### Vérification de la syntaxe

La commande suivante doit être utilisée avant l'execution d'un playbook afin d'en vérifier la syntaxe

```sh
ansible-playbook --syntax-check webserver.yml
```

### Lancement à blanc

L'option `-C` permet de faire un essai à blanc de l'exécution du playbook.

```sh
ansible-playbook -C webserver.yml
```
