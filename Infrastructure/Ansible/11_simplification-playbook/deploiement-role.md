# Déploiement de rôles avec Ansible Galaxy

[Ansible Galaxy](https://galaxy.ansible.com/): Bibliothèque publique de contenu Ansible.

## Outil en ligne de commande

### Recherche de rôles

```sh
ansible-galaxy search  search 'redis' --platforms EL
```

### Information détaillées sur un rôle

```sh
ansible-galaxy info
```

### Installation de rôles

```sh
ansible-galaxy install geerlingguy.redis -p roles/
```

### Installation de rôles à l'aide d'un fichier de configuration requise

Dans le réperoire `roles/requirements.yml` (sert de manifest de dépendance pour le projet de playbook)

```yml
- src: geerlingguy.redis
  version: "1.5.0"
```

### Installer les rôles via un fichier de rôles

```sh
ansible-galaxy install -r roles/requirements.yml -p roles
```

Exemple de fichier de configuration à l'aide de plusieurs sources 

```sh
$cat roles/requirements.yml
# from Ansible Galaxy, using the latest version
- src: geerlingguy.redis

# from Ansible Galaxy, overriding the name and using a specific version
- src: geerlingguy.redis
  version: "1.5.0"
  name: redis_prod

# from any Git-based repository, using HTTPS
- src: https://gitlab.com/guardianproject-ops/ansible-nginx-acme.git
  scm: git
  version: 56e00a54
  name: nginx-acme

# from any Git-based repository, using SSH
- src: git@gitlab.com:guardianproject-ops/ansible-nginx-acme.git
  scm: git
  version: master
  name: nginx-acme-ssh

# from a role tar ball, given a URL;
#   supports 'http', 'https', or 'file' protocols
- src: file:///opt/local/roles/myrole.tar
  name: myrole
```

### Rôles disponibles en local

```sh
ansible-galaxy list
```

### Supprimer un rôle

```sh
ansible-galaxy remove nginx-acme-ssh
```
