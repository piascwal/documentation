# Inventaires dynamiques

L'emplacement de l'inventaire dynamique est directement spécifier dans les fichier **ansible.cfg** ou via l'option **-i**.

:bulb: Si le fichier est éxécutable, il est traité pour générer l'inventaire. S'il ne l'est pas il est traité comme un inventaire statique.

:bulb: Un ensemble de scripts fournis par la communauté peut être trouvé [ici](https://github.com/ansible/ansible/tree/devel/contrib/inventory).

## Ecriture de programmes d'inventaire dynamiques

- N'importe quel langage de programmation
- Doit renvoyer des informations d'inventaire au format JSON
- La commande `ansible-inventory -i my-inventory --list` peut être utilisé pour visualiser l'inventaire courant au format JSON
- Le script doit commencer par le "sheban" approprié (ex: #!/usr/bin/python)
- Le script doit être exécutable pour être lancé par Ansible
- Le script doit prendre en compte l'option `--list`` qui renvoit tous les hôtes de l'inventaire vers la sortie standard
- Le script doit prendre en compte l'option `--host managed-host` qui renvoie le JSON composé des variables qui doivent être associées à cet host. S'il n'y en a pas, renvoie un JSON vide

## Inventaires multiples

- Ansible combine les inventaires si un répertoire contenant des inventaires statiques ou dynamiques est spécifié
- Les fichiers d'inventaires ne doivents pas dépendre d'autres fichiers d'inventaire pour être résolus
- L'ordre d'analyse des fichiers d'inventaire n'est pas spécifié par Ansible
- Certains fichiers d'un répertoire terminant par un certain *suffixe* peuvent être ignorés grâche à la directive `inventory_ignore_extensions` dans le fichier de configuration

:notebook: Documentation

- [Working With Dynamic Inventory](https://docs.ansible.com/ansible/2.7/user_guide/intro_dynamic_inventory.html)
- [Developing dynamic inventory](https://docs.ansible.com/ansible/latest/dev_guide/developing_inventory.html)
