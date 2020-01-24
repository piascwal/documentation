# Automatisation avec Ansible

## Général

- Platfeorme d'automatisation open source
- Décrit l'infrastructure informatique dans des playbook
- Automatisation lisible par l'homme -> écrit en yaml
- Aucune compétence en développement nécessaire
- Aucun agent installé sur les machines
- Les modules poussés sont immédiatement supprimés arprès execution
- Cross platform : Linux, Windows, UNIX, cloud, container...
- Simple à versionner, car sous forme de fichier plat
- Les inventaires de machines peuvent être statics comme dynamique
- Orchestrastion simple avec d'autre systems : Puppet, Jenkins, RedHat Satellite

## Concepts

### Idempotence

Si l'on joue un playbook plusieurs fois sur un même host. le résultat doit toujours être le même.

### Architecture

- 2 types de machines:
  - **Control nodes** : C'est là où ansible est installé et exécuté
  - **Managed Host** : Ce sont les machines listées dans l'inventaire

- Agentless
  - Ansible, se connecte en ssh dépose et execute les modules, puis les supprime. Il n'est donc pas nécessaire d'avoir ansible sur les machines

### Cas d'utilisation

- Configuration centralisée
- Déploiement d'application du dev à la prod
- Provisioning
- Déploiement continu
- Sécurité
- Orchestration

## L'inventaire

- C'est une collection d'hôtes
- Ces hôtes peuvent être affecté à des *groupes* qui peuvent être géré *collectivement*
- Les groupes peuvent contenir des groupes enfant
- Les hôtes peuvent être membre de plusieurs groupes
-L'inventaire peu définir des variables applicables au groupes en aux hôtes

*Exemple : Le groupe USA comporte 2 hôtes, le groupe canada comporte 2 hôtes et le groupe north-america regroupe le groupe canada et usa.*

```sh
[usa]
washington1.example.com
washington2.example.com

[canada]
ontario01.example.com
ontario02.example.com

[north-america:children]
canada
usa
```

- Simplification par plages

```sh
[START:END]
```

Les plages correspondent à toutes les valeurs comprises entre *START* et *END* incluses. Par exemple :

**192.168.[4:7].[0:255]** va correspondre à toutes les adresses IPv4 sur le réseau **192.168.4.0/22 (de 192.168.4.0 à 192.168.7.255)**.

**server[01:20].example.com** va correspondre à tous les hôtes nommés **server01.example.com** jusqu'à **server20.example.com**.

**[a:c].dns.example.com** va correspondre aux hôtes nommés **a.dns.example.com**,**b.dns.example.com** et **c.dns.example.com**.

**2001:db8::[a:f]** va correspondre à toutes les adresses IPv6 **de2001:db8::a** à **2001:db8::f**.

- Tester l'inventaire
  - Lister tous les hosts d'un groupe (exemple: canada)

    ```sh
    ansible canada --list-hosts
    ```
  
  - Présence d'une machine

    ```sh
    ansible washington1.example.com --list-hosts
    ```

- Emplacement par défaut de l'inventaire `/etc/ansible/hosts`, mais la bonne pratique dit qu'il ne faut pas utiliser ce répertoire

## Configuration

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

### Paramètres

Les paramètres de configuration sont définis en tant que clé-valeur et groupés pas sections :

- `[defaults]` : Emplacement de la plus part des paramètres
- `[privilege_escalation]` : Définition de l'execution des opération imposant l'augementation de privilège
- `[paramiko_connection]`, `[ssh_connection]`, `[accelerate]` : Optimisation des connexions aux hôtes
- `[selinux]`: Configuration des interactions SELinux

#### Exemple de clé-valeur

##### répertoire ou fichier de l'inventaire (static ou script dynamique)
  
  ```sh
  [defaults]
  inventory = ./inventory
  ```

##### Paramètre de connexion

  ```sh
  [defaults]
  # Utilisateur distant si différent de celui de l'utilisateur local
  remote_user = root
  # Demande demander à le mdp à l'utilisateur local si Ansible n'a pas de clé permettant de s'authentifier
  ask_pass = true
  ```

###### Connexion par paire de clé public/privée

Pour pouvoir passer le paramètre **ask_pass** à **false**

- Vérifier que l'utilisateur sur le noeud de contrôle à une paire de clé SSH dans **~/.ssh** en exécutant la commande `ssh-keygen`

- Pour un seul hôte géré existant, vous pouvez installer votre clé publique sur l'hôte géré et remplir votre fichier **~/.ssh/known_hosts** local avec sa clé d'hôte à l'aide de la commande `ssh-copy-id` :

  ```sh
    ssh-copy-id root@web1.example.com
  ```

##### Augmentation des privilège

  ```sh
  [privilege_escalation]
  # Activer l'augmentation des privilèges par défaut
  become = true
  become_method = sudo
  become_user = root
  become_ask_pass = false
  ```

## Commande ad hoc

Permet de lancer une **commande simple**, que l'on a pas besoin de sauvegarder pour l'executer plus tard, sans nécessité d'écrire un playbook.

Exemple de cas d'utilisation:

- Vérifier qu'une ligne existe dans /etc/hosts
- redémarrer un service
- vérifier qu'un package logiciel est à jour

### Passer des arguments

Les arguments sont passés via l'option `-a`

**Exemple1** : vérifier que l'utilisateur toto a bien l'UID 4000, grâce au module *user*

```sh
ansible -m user -a 'name=toto uid=4000 state=present' servera.lab.example.com
```

**Exemple2** : executer la commande `hostname`sur le group my-hosts

```sh
ansible my-hosts -m command -a '/usr/bin/hostname'
```

## Playbook

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

### Execution de playbook

La commande à utilisé est la suivante :

```sh
ansible-playbook site.yml
```

#### Augmentation du niveau de verbosité

Il existe 4 niveau de verbosité pouvant être appliqué via les options suivantes:

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

#### Vérification de la syntaxe

La commande suivante doit être utilisé avant l'execution d'un playbook afin d'en vérifier la syntaxe

```sh
ansible-playbook --syntax-check webserver.yml
```

#### Lancement à blanc

L'option `-C` permet de faire un essai à blanc de l'exécution du playbook.

```sh
ansible-playbook -C webserver.yml
```

## Gestion des variables

### Convention de nommage

La convention de nommage à respecter est le `snake_case`.
Convention nottament utilisé  sous le langage Python.
Une variable doit donc respecter les points suivants:

- Commence par une lettre.
- Peut contenir des chiffres
- Mots séparés par des tirets bas : `_`

### Où déclarer les variables

3 niveau de propagation de variable :

- **globale** : variables définies à partir d'une ligne de commande ou d'une configuration Ansible

- **play** : variables définies dans le play et les structures correspondantes

- **hôtes** : variables définies sur des groupes d'hôtes et des hôtes individuels par l'inventaire, une collecte de faits ou des tâches enregistrées

:warning: Les variables sont traitées avec hierarchie.
De la plus spécifique à la moins spécifique.

### Variables dans les playbook

#### Bloc vars

Couramment les variables sont déclarées dans un bloc `vars`en début de playbook

```yml
- hosts: all
  vars:
    user: toto
    home: /home/toto
```

#### Fichier externe

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

### Variables d'hôte et de groupe

Les variables d'**hôtes** écrasent les variables de **groupe**.

#### Varible d'hôte (obsolète)

```sh
[servers]
demo.example.com  ansible_user=toto
```

#### Variable de groupe

:warning: **Non recommendé : Rend le fichier inventory moins lisible**

```sh
[servers]
demo1.example.com
demo2.example.com

[servers:vars]
user=toto
```

#### Répertoires group_vars et host_vars

La bonne pratique consiste à déclarer les variables de groupes et de host dans les répertoires **group_vars** et **host_vars**

Exemple: pour définir des variables pour le groupe `servers` il faut créer le fichier nommé `group_vars/servers` puis ajouter le contenu sous le format:

```yml
user: toto
```

### Variable supplémentaire (ligne de commande)

Le type de variable le plus prioritaire est celui transmis en argument aux commandes **ansible** et **ansible-playbook**. Elles sont appelées variables supplémentaires

Exemple via l'option `-e`:

```sh
ansible-playbook main.yml -e "package=apache"
```

### Utilisation de la variable

Le format moustache est utilisé pour l'interprétation des variables `{{ ma_variable }}`

:warning: Si la variables est utilisé comme 1er  élément commençant une valeur, il est nécessaire d'utilisé les guillemets:

Exemple :

 ```sh
    with_items:
      - "{{ foo }}"
 ```

### Variables et matrices

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

#### Utilisation des variables Matriciel

##### 1ere méthode

```yml
# Rtourne 'Pierre'
users.pdupont.first_name

# Rtourne '/users/jmartin'
users.jmartin.home_dir
```

##### 2ere méthode

```yml
# Rtourne 'Pierre'
users['pdupont']['first_name']

# Rtourne '/users/jmartin'
users['jmartin']['home_dir']
```

### Variables enregistrées

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

## Gestion des secrets

### Création d'un fichier chiffré

- Création simple par saisie de mot de passe

```sh
$ ansible-vault create secret.yml
New Vault password: mdp
Confirm New Vault password: mdp
```

- Création utilisant un fichier de mot de passe.
Il est à notre charge de protéger ce fichier de mdp

```sh
ansible-vault create --vault-password-file=vault-pass secret.yml
```

### Visionner un fichier chiffré

```sh
$ansible-vault view secret1.yml
Vault password: mdp
```

### Modification d'un fichier chiffré

```sh
$ansible-vault edit secret.yml
Vault password: mdp
```

### Chiffrer un fichier existant

```sh
$ansible-vault encrypt secret1.yml secret2.yml
New Vault password: mdp
Confirm New Vault password: mdp
Encryption successful
```

### Déchiffrer un fichier existant

```sh
$ansible-vault decrypt secret1.yml --output=secret1-decrypted.yml
Vault password: mdp
```

### Modification du Mot de passe d'un fichier chiffré

- Simple

```sh
$ansible-vault rekey secret.yml
Vault password: mdp
New Vault password: mdp2
Confirm New Vault password: mdp2
Rekey successful
```

- Par fichier

```sh
ansible-vault rekey --new-vault-password-file=NEW_VAULT_PASSWORD_FILE secret.yml
```

### Playbook utilisant des fichiers chiffrer

Il est nécessaire de fournir le mot de passe au playbook:

- simple

```sh
$ansible-playbook --vault-id @prompt site.yml
Vault password (default): mdp
```

- Par fichier

```sh
ansible-playbook --vault-password-file=vault-pw-file site.yml
```

## Faits Ansible

Ce sont des variables automatiquement détectées par Ansible sur un hôte géré.
Les faits contiennent des informations prores à l'hôte qui peuvent être utilisées comme des variables dans les plays, conditions, boucles.

Exemple :

- le nom de l'hôte
- la version du noyau
- les interfaces réseau
- les adresses IP
- la version du système d'exploitation
- différentes variables d'environnement
- le nombre d'UC
- la mémoire disponible
- l'espace disque disponible

### La tâche Gathering Facts

La tâche `Gathering Facts` exécuté en début de playbook a pour rôle d'executer automatiquement le module `setup`afin de rassembles les `faits`.

### Afficher les faits des hôtes gérés

Exemple de playbook:

```yml
- name: Fact dump
  hosts: all
  tasks:
    - name: Print all facts
      debug:
        var: ansible_facts
```

Exemple 2 :

```yml
---
- hosts: all
  tasks:
  - name: Prints various Ansible facts
    debug:
      msg: >
        L'adresse IPv4 de {{ ansible_facts.fqdn }}
        est {{ ansible_facts.default_ipv4.address }}
```

exemple de faits :

| Fait | Variable |
| ------ | ------ |
| Nom d'hôte abrégé |ansible_facts['hostname'] |
| Nom de domaine complet |ansible_facts['fqdn'] |
| Adresse IPv4 principale (en fonction du routage) | ansible_facts['default_ipv4']['address'] |
| Liste des noms de toutes les interfaces réseau |ansible_facts['interfaces'] |
|Taille de la partition de disque /dev/vda1 |ansible_facts['devices']['vda']['partitions']['vda1']['size'] |
| Liste des serveurs DNS |ansible_facts['dns']['nameservers'] |
| Version du noyau en cours d'exécution | ansible_facts['kernel']

### Désactiver les fait sur un play

utiliser l'instruction suivante `gather_facts: no`:

```yml
---
- name: This play gathers no facts automatically
  hosts: large_farm
  gather_facts: no
```

:bulb: le module `setup` peut être utilisé pour manuellement collecter les faits même s'ils ont été désactivés

### Faits personnalisés

- Ils sont stocker localement sur chaque hôte
- Il sont inclus à la liste des faits standards
- Ils sont collectés par le module setup
- Ils sont définis dans un fichier statique
- Ils sont au format INI ou JSON
- Ils peut sagir de scripts exécutable générant du JSON

Par défaut le module setup charge les les faits personnalisés depuis les fichiers de scripts du dossier `/etc/ansible/facts.d`de chaque hôte.
Le nom de chaque fichier ou script doit se terminer par `.fact`

Exemple format INI:

```ini
[packages]
web_package = httpd
db_package = mariadb-server

[users]
user1 = joe
user2 = jane
```

Exemple format JSON

```json
{
  "packages": {
    "web_package": "httpd",
    "db_package": "mariadb-server"
  },
  "users": {
    "user1": "joe",
    "user2": "jane"
  }
}
```

:bulb: Les faits personnalisés sont stockés par le module `setup`dans la variable `ansible_facts.ansible_local`

Exemple
si le fait se trouve dans le répertoire `/etc/ansible/facts.d/custom.facts`.
La valeur de `ansible_facts.ansible_local['custom']['users']['user1']` est égale à `joe`

### Variables magiques

Ce ne sont pas des faits, mais sont configurées automatiquement par Ansible.

Les plus communes :

- **hostvars** : Contient les variables pour les hôtes gérés et peut être utilisée afin d'obtenir les valeurs des variables d'un autre hôte géré. Cela n'inclut pas les faits de l'hôte géré s'ils n'ont pas encore été collectés pour cet hôte

- **group_names** : Liste de tous les groupes dans lesquels se trouve l'hôte géré actuel.

- **groups** : Liste de tous les groupes et hôtes de l'inventaire.

- **inventory_hostname** : Contient le nom d'hôte de l'hôte géré actuel tel que configuré dans l'inventaire. Il peut, pour différentes raisons, être différent du nom d'hôte signalé par les faits.

Pour les autres variables magiques  consulter ce [lien](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable).

## Contrôle des tâches

### Boucles et conditions

#### Boucles

Mot clé à utiliser : **loop**

Exemple :

```yml
- name: Postfix et Dovecot tournent
  service:
    name: "{{ item }}"
    state: started
  loop:
    - postfix
    - dovecot
```

Exemple avec une boucle sur **une variable**:

```yml
vars:
  mail_services:
    - postfix
    - dovecot

tasks:
  - name: Postfix and Dovecot are running
    service:
      name: "{{ item }}"
      state: started
    loop: "{{ mail_services }}"
```

Exemple avec une boucle sur une **liste complexe**:

```yml
- name: Users exist and are in the correct groups
  user:
    name: "{{ item.name }}"
    state: present
    groups: "{{ item.groups }}"
  loop:
    - name: jane
      groups: wheel
    - name: joe
      groups: root
```

:bulb: Dans les version antérieur était utilisé d'autre mots clefs : **with_items**, **with_file**, **with_sequence**


## Memo

### Commande ansible

- Vérifier que l'on peut correctement exécuter les modules Ansible utilisant Python sur les hôtes:

    ```sh
    ansible my-group -m ping
    ```

- Lister tous les modules installés sur le system

    ```sh
    ansible-doc -l
    ```

- Consulter la doc d'un module

    ```sh
    ansible-doc ping
    ```

## Bon à savoir

- Si on utilise `vi` pour modifier des yml, il est nécessaire d'ajouter dans le fichier `~/.vimrc` la ligne suivante pour que les tabulations dans les fichiers yml soient transformées en double espace :

  ```sh
  autocmd FileType yaml setlocal ai ts=2 sw=2 et
  ```

- Eviter d'utiliser les modules `command`, `shell` et `raw` car ils utilisent des commandes arbitraire et il est très facile d'écrire des playbook **non-idempotent**
