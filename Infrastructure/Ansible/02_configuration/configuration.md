# Configuration

Les fichiers de configuration ansible est recherché selon l'ordre suivante:

- variable d'environnement `$ANSIBLE_CONFIG`
- ./ansible.cfg
- ~/.ansible.cfg
- /etc/ansible/ansible.cfg

Pour vérifier que fichier de configuration est utilisé :

```sh
ansible --version

 # ou

ansible servers --list-hosts -v
```

## Paramètres

Les paramètres de configuration sont définis en tant que clé-valeur et groupés pas sections :

- `[defaults]` : Emplacement de la plus part des paramètres
- `[privilege_escalation]` : Définition de l'execution des opération imposant l'augementation de privilège
- `[paramiko_connection]`, `[ssh_connection]`, `[accelerate]` : Optimisation des connexions aux hôtes
- `[selinux]`: Configuration des interactions SELinux

### Exemple de clé-valeur

#### répertoire ou fichier de l'inventaire (static ou script dynamique)
  
  ```sh
  [defaults]
  inventory = ./inventory
  ```

#### Paramètre de connexion

  ```sh
  [defaults]
  # Utilisateur distant si différent de celui de l'utilisateur local
  remote_user = root
  # Demande demander à le mdp à l'utilisateur local si Ansible n'a pas de clé permettant de s'authentifier
  ask_pass = true
  ```

##### Connexion par paire de clé public/privée

Pour pouvoir passer le paramètre **ask_pass** à **false**

- Vérifier que l'utilisateur sur le noeud de contrôle à une paire de clé SSH dans **~/.ssh** en exécutant la commande `ssh-keygen`

- Pour un seul hôte géré existant, vous pouvez installer votre clé publique sur l'hôte géré et remplir votre fichier **~/.ssh/known_hosts** local avec sa clé d'hôte à l'aide de la commande `ssh-copy-id` :

  ```sh
    ssh-copy-id root@web1.example.com
  ```

#### Augmentation des privilège

  ```sh
  [privilege_escalation]
  # Activer l'augmentation des privilèges par défaut
  become = true
  become_method = sudo
  become_user = root
  become_ask_pass = false
  ```
