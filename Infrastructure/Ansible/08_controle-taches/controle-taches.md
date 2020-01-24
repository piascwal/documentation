# Contrôle des tâches

## Boucles et conditions

### Boucles

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
