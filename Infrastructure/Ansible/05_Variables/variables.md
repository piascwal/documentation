# Gestion des variables

## Convention de nommage

La convention de nommage à respecter est le `snake_case`.
Convention nottament utilisé  sous le langage Python.
Une variable doit donc respecter les points suivants:

- Commence par une lettre.
- Peut contenir des chiffres
- Mots séparés par des tirets bas : `_`

## Où déclarer les variables

3 niveau de propagation de variable :

- **globale** : variables définies à partir d'une ligne de commande ou d'une configuration Ansible

- **play** : variables définies dans le play et les structures correspondantes

- **hôtes** : variables définies sur des groupes d'hôtes et des hôtes individuels par l'inventaire, une collecte de faits ou des tâches enregistrées

:warning: Les variables sont traitées avec hiérarchie.
De la plus spécifique à la moins spécifique.

## Variables dans les playbook

### Bloc vars

Couramment les variables sont déclarées dans un bloc `vars`en début de playbook

```yml
- hosts: all
  vars:
    user: toto
    home: /home/toto
```

### Fichier externe

- Playbook

```yml
- hosts: all
  vars_files:
    - vars/users.yml
```

- users.yml

```yml
user: toto
home: /home/toto
```

## Variables d'hôte et de groupe

Les variables d'**hôtes** écrasent les variables de **groupe**.

### Varible d'hôte (obsolète)

```sh
[servers]
demo.example.com  ansible_user=toto
```

### Variable de groupe

:warning: **Non recommendé : Rend le fichier inventory moins lisible**

```sh
[servers]
demo1.example.com
demo2.example.com

[servers:vars]
user=toto
```

### Répertoires group_vars et host_vars

La bonne pratique consiste à déclarer les variables de groupes et de host dans les répertoires **group_vars** et **host_vars**

Exemple: pour définir des variables pour le groupe `servers` il faut créer le fichier nommé `group_vars/servers` puis ajouter le contenu sous le format:

```yml
user: toto
```

## Variable supplémentaire (ligne de commande)

Le type de variable le plus prioritaire est celui transmis en argument aux commandes **ansible** et **ansible-playbook**. Elles sont appelées variables supplémentaires

Exemple via l'option `-e`:

```sh
ansible-playbook main.yml -e "package=apache"
```

## Utilisation de la variable

Le format moustache est utilisé pour l'interprétation des variables `{{ ma_variable }}`

:warning: Si la variables est utilisé comme 1er  élément commençant une valeur, il est nécessaire d'utilisé les guillemets:

Exemple :

 ```sh
    with_items:
      - "{{ foo }}"
 ```

## Variables et matrices

L'utilisation de matrice permet de simplifier la lisibilité et permet le parcours hierarchique:

Exemple : pour deux utilisateurs avec nom, prenom, homedir

Le bloc à simplifier est le suivant:

```yml
user1_first_name: Pierre
user1_last_name: Dupont
user1_home_dir: /users/pdupont
user2_first_name: Jean
user2_last_name: Martin
user3_home_dir: /users/jMartin
```

Le résultat sous forme matriciel :

```yml
users:
  pdupont:
    first_name: Pierre
    last_name: Dupont
    home_dir: /users/pdupont
  jmartin:
    first_name: Jean
    last_name: Martin
    home_dir: /users/jmartin
```

### Utilisation des variables Matriciel

#### 1ere méthode

```yml
# Rtourne 'Pierre'
users.pdupont.first_name

# Rtourne '/users/jmartin'
users.jmartin.home_dir
```

#### 2ere méthode

```yml
# Rtourne 'Pierre'
users['pdupont']['first_name']

# Rtourne '/users/jmartin'
users['jmartin']['home_dir']
```

## Variables enregistrées

Il est aussi possible de capturer le résultat d'une commande via l'instruction `register`

Exemple :

```yml
---
- name: Installe le package et affiche le résultat
  hosts: all
  tasks:
    - name: Installe le package
      yum:
        name: httpd
        state: installed
      register: install_result

    - debug: var=install_result
```
