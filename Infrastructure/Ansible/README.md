# Automatisation avec Ansible

## Général

- Plateforme d'automatisation open source
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

## Table des matières

- [L'inventaire](./01_inventaire/inventaire.md)
- [Configuration](./02_configuration/configuration.md)
- [Commendes ad hoc](./03_commande-ad-hoc/command.md)
- [Playbook](./04_playbook/playbook.md)
- [Variables](./05_variables/variables.md)
- [Secrets](./06_secrets/secrets.md)
- [Faits](./07_faits/faits.md)
- Contrôle des tâches
  - [Boucles et conditions](./08_controle-taches/boucles-conditions.md)
  - [Gestionnaires (Handlers)](./08_controle-taches/gestionnaires.md)
  - [Gestion des échecs de tâche](./08_controle-taches/echecs-tache.md)
  - [Déploiement de fichiers sur les hôtes](./09_deploiement_fichiers_hotes/deploiement_fichiers_hotes.md)
- [Déploiement de fichiers personnalisés avec des modèles Jinja2](./09_deploiement_fichiers_hotes/fichiers-perso-jinja2.md)
- Projets Volumineux
  - [Inventaires dynamiques et multi-inventaires](./10_projets-volumineux/inventaire-dynamique.md)
  - [Parallélisme](./10_projets-volumineux/parallelisme.md)
  - [Inclusion Importation fichier](./10_projets-volumineux/inclusion_importation_fichiers.md)
-Simplification des Playbook avec des rôles
  - [Strucutre d'un rôle](./11_simplification-playbook/structure-role.md)
  - [Création de rôles](./11_simplification-playbook/creation-role.md)
  - [Déploiement de rôles (Ansible Galaxy)](./11_simplification-playbook/deploiement-role.md)
  - [Rôle système](./11_simplification-playbook/role-systeme.md)
- [Résolution des problèmes liés à Ansible](./12_problemes-ansible/problemes-ansible.md)

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

#### Inventaire

##### Groupes spéciaux

- **all** ou **\*** : Tous les hôtes de l'inventaires
- **ungrouped** : Tous les hôtes n'appartenant à aucun groupe

#### Playbook

##### Directive hosts

- ***.example.com** : tous les hôtes ou groupes d'hôtes se terminant par example.com
- **toto,tata,192.167.23.35** : tous les hôtes du groupe toto et tata ainsi que l'hôst 192.167.23.35
- **lab,&datacenter1**: Utilisation du `&`en tant que **AND logique**. Ici les machines du groupe `lab` uniquement si elles se trouvent également dans le groupe `datacenter1`
- **datacenter,!test2.example.com**: Utilisation du '!' en tant que **NOT logique**. ICi tous les hôtes définis dans le groupe datacenter, à l'exception de test2.example.com

#### Liste des faits associés à un hôte

```sh
ansible system_hostname -i inventory_file -m setup
```

## Bon à savoir

- Si on utilise `vi` pour modifier des yml, il est nécessaire d'ajouter dans le fichier `~/.vimrc` la ligne suivante pour que les tabulations dans les fichiers yml soient transformées en double espace :

  ```sh
  autocmd FileType yaml setlocal ai ts=2 sw=2 et
  ```

- Eviter d'utiliser les modules `command`, `shell` et `raw` car ils utilisent des commandes arbitraire et il est très facile d'écrire des playbook **non-idempotent**

- utiliser le module **parted** pour vous assurer qu'un disque est correctement partitionné ;

utiliser le module **lvg** pour gérer les groupes de volumes et les volumes physiques LVM ;

utiliser le module **lvol** pour gérer les volumes logiques LVM ;

utiliser le fait **ansible_facts['lvm']** pour obtenir des informations sur les paramètres LVM existants sur les hôtes gérés ;

utiliser le module **filesystem** pour formater les périphériques de bloc avec des systèmes de fichiers ;

utiliser le module **mount** pour ajouter des entrées pour vos systèmes de fichiers dans **/etc/fstab** et les monter immédiatement.

## Documentations

[Packaging Modules](https://docs.ansible.com/ansible/latest/modules/list_of_packaging_modules.html)
[System Modules](https://docs.ansible.com/ansible/latest/modules/list_of_system_modules.html)
[Identity Modules](https://docs.ansible.com/ansible/latest/modules/list_of_identity_modules.html)
