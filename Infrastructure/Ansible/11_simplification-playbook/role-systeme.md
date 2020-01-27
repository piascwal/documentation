# Rôle système

À partir de Red Hat Enterprise Linux 7.4, un certain nombre de rôles Ansible ont été fournis avec le système d'exploitation dans le paquetage rhel-system-roles via le canal de distribution Extras.

Ces rôles ont pour objectif de normaliser la configuration des sous-système RHEL sur plusieurs versions à partir de la version 6.10.

Exemple :

Service de synchronisation horloge recommandé:

- RHEL 7 = *chronyd*
- RHEL 6 = *ntpd*

Avec le rôles système RHEL les admin peuvent utiliser le rôle **rhel-system-roles.timesync** pour configurer la synchronisation des horloges des hôtes RHEL 6 et 7

## Installation des rôles système RHEL

Prérequis:

- Le nœud de contrôle est enregistré dans un abonnement Red Hat Enterprise Linux
- Ansible Engine est installé

Procédure:

- Activer le canal de distribution Extras:

    ```sh
    subscription-manager repos --enable rhel-7-server-extras-rpms
    ```

- Installez les rôles système RHEL.

    ```sh
    yum install rhel-system-roles
    ```

Les rôles système RHEL sont situés dans le répertoire `/usr/share/ansible/roles`.
