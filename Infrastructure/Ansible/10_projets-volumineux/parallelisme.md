# Configuration d'un parallélisme

- Gestion du nombre de connexion simultanées ouvertes par Ansible sur les hôtes.
- Déterminer comment Ansible traite les groupes d'hôtes via les tâches du play.

## Configuration via les forks

Utiliser la directive `forks` dans le fichier **ansible.cfg** pour déterminer le nombre maximal de connexion simultanées:

```txt
forks = 5
```

:bulb: Si les hôtes sons sous Linux, la plus part des tâches s'exécutent sur les hôtes. Ainsi le noeud de contrôle est moins chargé et la valeur du fork peut être beaucoup plus élevée (aux alentours de 100).