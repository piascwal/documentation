# Ansible Tower

Objectifs:

- Gestion de projet Ansible existants
- Utiliser un tableau de bord centraliser pour lancer, controler et monitorer les jobs Ansible
- Configurer les utilisateurs et équipes  accédant au systems, projets et autres ressources
- Ordonnancer des jobs Ansible et mettre à jour l'inventaire de jobs
- Effectuer de la maintenance et de l'administration de Ansible Tower
- Utiliser les API d'ANsible tower pour lancer les jobs depuis des templates existants

## Pouquoi Ansible Tower

Pour avoir un framework pour lancer et gérer Ansible efficacement à l'échelle d'une entreprise.

- Centralisation de la gestion des playbook
- Contrôles basés sur des rôles (RBAC)
- Centralisation des logs et audites
- Mise à disposition d'API pour une intégration faciles avec les outils d'entreprise existants ( Jenkins, CloudForms, Red Hat Satellite) dnas le cadre d'une CI/CD
- Mechanisme de centralisation d'utilisation et de contrôle des credentials machine et autres secrets sans les exposés aux utilisateurs finaux d'Ansible Tower.

:bulb: Toute action réalisable via l'interface web est réalisable via l'API

Les données d'Ansible Tower sont stockées dans une base **PostgreSQL** et utilise **RabbitMQ** comme system d'envoie de message.

## Fonctionnalités d'Ansible Tower

- Visualisation de tableau de bord
- Contrôle d'accès basé sur les rôles
- Gestion graphique de l'inventaire
- Ordonnancement de Job
- Rapport de Job en temps réel et historisé
- Automatisation déclenchée par l'utilisateur à la demande
- Execution de commandes à distance
- Gestion des crédentials
- Audit et Log centralisés
- Intégration de notifications
- Workflows Multiplaybook
- Restful API
