# Boucles et conditions

## Boucles

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

### Variables de registre avec des boucles

Le mot clé `register` peut aussi récupérer le résultat de tâche en boucle.

Exemple:

```yml
---
- name: Loop Register Test
  gather_facts: no
  hosts: localhost
  tasks:
    - name: Looping Echo Task
      shell: "echo This is my item: {{ item }}"
      loop:
        - one
        - two
      register: echo_results

    - name: Show echo_results variable
      debug:
        var: echo_results
```

## Conditions

Le mot cké à utiliser est : **when**

Exemple très simple : On déclar une variable `run_my_task` à `true`.
la tâche est executer qye sur `run_my_task`est à `true`

```yml
---
- name: Simple Boolean Task Demo
  hosts: all
  vars:
    run_my_task: true

  tasks:
    - name: httpd package is installed
      yum:
        name: httpd
      when: run_my_task
```

### Opérateurs

Liste des opérateurs classique:  *==, <, >, <=, >=, !=*

- valeur boolean est true : ***ma_variable***
- valeur boolean est false: **not *ma_variable***
- Variable existe: ***ma_variable* is defined**
- variable n'existe pas: ***ma_variable* is not defined**
- La valeur de ma variable existe dans une liste : ***ma_variable* in *ma_list***

### Multiple conditions

- Et :

```yml
when: ansible_distribution_version == "7.5" and ansible_kernel == "3.10.0-327.el7.x86_64"
```

- Ou :

```yml
when: ansible_distribution == "RedHat" or ansible_distribution == "Fedora"
```

:bulb: Si une liste est fourni au `when` la condition est considéré comme un **and**.
Ce format est **recommandé** par ansible pour améliorer la lisibilité.
Exemple:

```yml
when:
  - ansible_distribution_version == "7.5"
  - ansible_kernel == "3.10.0-327.el7.x86_64"
```

## Longues conditions

Il est possible d'utiliser des parenthèses pour combiner les condition

Exemple:

```yml
when: >
    ( ansible_distribution == "RedHat" and
      ansible_distribution_major_version == "7" )
    or
    ( ansible_distribution == "Fedora" and
    ansible_distribution_major_version == "28" )
```

:bulb: On remarquera le caractère plus grand que (**>**), utilisé pour pouvoir diviser la condition sur plusieurs lignes.


## Combinaison de boucles et de conditions

Exemple : On install mariadb que si le système de fichier est monté sur / avec plus de 300Mo libre. (ansible_mounts est un fait représentant une liste de dictionnaire)

```yml
- name: install mariadb-server if enough space on root
  yum:
    name: mariadb-server
    state: latest
  loop: "{{ ansible_mounts }}"
  when: item.mount == "/" and item.size_available > 300000000
```
