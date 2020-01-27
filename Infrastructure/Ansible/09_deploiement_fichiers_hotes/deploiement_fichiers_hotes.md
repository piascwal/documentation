# Déploiement de fichiers sur les Hôtes

L'objectif est d'être en capacité de CRUD des fichiers sur les hôtes, ainsi que de gérer les autorisations et habilitation.

## Module Files

Les modules **Files** permettent d'accomplir les tâches de gestion de fichiers Linux.

Exemple: **Vérifier l'existence d'une fichier** :

```yml
- name: Touch a file and set permissions
  file:
    path: /path/to/file
    owner: user1
    group: group1
    mode: 0640
    state: touch
```

Exemple: **Copy un fichier local vers un fichier sur l'hôte** avec le module `copy`

```yml
- name: Copie d'un fichier local sur un hôte
  copy:
    src: file
    dest: /path/to/file
```

:warning: Par défaut le module est configuré avec `force: yes`, qui va écraser le fichier distant s'il existe mais qu'il contient un contenu différent.
Si `force: no` le fichier est copié seulement s'il n'existe pas.

Exemple: **S'assurer qu'une ligne est présente dans un fichier existant** avec le module `lineinfile`

```yml
- name: Ajout d'une seule ligne dans une fichier
  lineinfile:
    path: /path/to/file
    line: 'Ajout de cette ligne dans le fichier'
    state: present
```

Exemple: **S'assurer qu'un bloc est présente dans un fichier existant** avec le module `blockinfile`

```yml
- name: Ajout de plusieurs lignes dans un fichier
  blockinfile:
    path: /path/to/file
    block: |
      Première ligne à ajouter
      Deuxième ligne à ajouter
state: present
```

Exemple: **Suppression d'un fichier** avec le module `file` et l'option `state: absent`

```yml
- name: S'assurer que le fichier n'existe pas sur les hôtes
  file:
    dest: /path/to/file
    state: absent
```

Exemple: **Récupérer de l'état d'un fichier depuis un hôte** avec le module `state`

```yml
- name: Vérifier le checksum du fichier
  stat:
    path: /path/to/file
    checksum_algorithm: md5
  register: result

- debug
    msg: "Le checksum du fichier est {{ result.stat.checksum }}"
```

:bulb: Le module *state* récupère des faits pour un fichier.
Dans l'exemple le checksum MD5 est utilisé, mais il est aussi possible d'utiliser l'algo SHA256 plus moderne).

Exemple: **Synchronisation de fichiers entre noeud de contrôle et hôtes** avec le module `synchronize`

```yml
- name: synchronize local file to remote files
  synchronize:
    src: file
    dest: /path/to/file
```

:bulb: pour utiliser le module `synchronize` il est nécesaire que rsync soit installé sur les noeuds et les hôtes.

:notebook: Pour plus d'information, consulter la [documentation sur les modules files](https://docs.ansible.com/ansible/2.7/modules/list_of_files_modules.html)