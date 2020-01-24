# Gestionnaires (Handlers)

- Les gestionnaires, sont des tâches qui répondent à une notification déclenchée par une autre tâche.

- Les tâches notifient leur gestionnaire si et seulement si la tâche a modifier quelque chose sur l'hôte.

- Chaque gestionnaire a un nom globalement unique et est déclenché à la fin d'un bloc de tâche dans un playbook.

- Le gestionnaire  est exécuter une **unique** fois lorsque  toutes les tâches sont terminée. Celà si et seulement si une ou plusieurs tâche l'ont notifié.

- En général les gestionnaires, servent à **redémarrer les hôtes** et **relancer les services**

- Les gestionnaires peuvent être considérés comme des tâches innactives, déclenchée explicitement avec l'instruction `notify`

**Exemple** : Redémarrer le serveur Apache par le gestionnaire *restart apache* lorsqu'un fichier de conf est mis à jour et le notifie:

```yml
tasks:
  - name: copy demo.example.conf configuration template
    template:
      src: /var/lib/templates/demo.example.conf.template
      dest: /etc/httpd/conf.d/demo.example.conf
    notify:
      - restart apache

handlers:
  - name: restart apache
    service:
      name: httpd
      state: restarted
```

## :warning:  A garder à l'esprit lors de l'utilisation de gestionnaires

- Les gestionnaires fonctionnent toujours dans l'ordre spécifié dans la section handlers et **non pas** dans lequel ils sont listés par les instructions `notify`

- Le nom des gestionnaires existent dans un espace logique global. Si un même nom est attribué à 2 gestionnaires, le gestionnaire ne sera pas exécuté
