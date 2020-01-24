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
- [Boucles et conditions](./08_controle-taches/boucles-conditions.md)
- [Gestionnaires (Handlers)](./08_controle-taches/gestionnaires.md)
- [Gestion des échecs de tâche](./08_controle-taches/echecs-tache.md)

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
