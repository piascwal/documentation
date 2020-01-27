# Déploiement de fichiers personnalisés avec des modèles Jinja2

L'utilisation de modèle est un moyen beaucoup plus puissant pour gérer des fichiers. Cette méthode génère moins d'erreur que l'utilisation des modules classiques tels que *lineinfile* et *blockinfile*

## Jinja2

### Délimiteurs

- **{%Expression%}** : Synthaxe utilisée pour les expressions ou la logique
- **{{Expression}}** : Synthaxe utilisée pour générer le résultat d'une expression ou d'une variable.

Exemple : fichier.j2

```txt
Hostname : {{ ansible_facts.hostname }}.
Date du jour: {{ ansible_facts.date_time.date }}.
```

:bulb: Aucune extension n'est nécessaire pour les template. Mais il est recommandé d'en mettre une.

### Déploiement des modèles

Les déploiement des modèles est effectué à l'aide du module `template`

Exemple:

```yml
tasks:
  - name: Déploiement du template
    template:
      src: /tmp/j2-template.j2
      dest: /tmp/dest-config-file.txt
```

:bulb: Afin déviter que les fichiers déployés soient modifiés manuellement il est recommandé d'ajouter un commentaire au début du modèle, afin d'indiquer que le fichier ne doit pas être modifié.

### Boucles

Exemple : Itération sur les éléments présent dans la variables **users**

```txt
{% for user in users %}
      {{ user }}
{% endfor %}
```

Exemple : Itération avec condition : **Afficher tous les utilisateurs sauf root**

```txt
{% for myuser in users if not myuser == "root" %}
User number {{loop.index}} - {{ myuser }}
{% endfor %}
```

:bulb: La première valeur de `loop.index`est `1`

### Conditions

Exemple Simple:

```txt
{% if finished %}
{{ result }}
{% endif %}
```

### Filtres

Utilisation du `|` et des fonctions de filtre

```txt
{{ output | to_json }}
{{ output | to_yaml }}
{{ output | to_nice_json }}
{{ output | to_nice_yaml }}
{{ output | from_json }}
{{ output | from_yaml }}
```

:notebook: Documentation

- [Template](https://docs.ansible.com/ansible/2.7/modules/template_module.html)
- [Variables](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_variables.html)
- [Filtres](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)
