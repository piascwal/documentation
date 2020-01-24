# L'inventaire

- C'est une collection d'hôtes
- Ces hôtes peuvent être affecté à des *groupes* qui peuvent être géré *collectivement*
- Les groupes peuvent contenir des groupes enfant
- Les hôtes peuvent être membre de plusieurs groupes
-L'inventaire peu définir des variables applicables au groupes en aux hôtes

*Exemple : Le groupe USA comporte 2 hôtes, le groupe canada comporte 2 hôtes et le groupe north-america regroupe le groupe canada et usa.*

```sh
[usa]
washington1.example.com
washington2.example.com

[canada]
ontario01.example.com
ontario02.example.com

[north-america:children]
canada
usa
```

- Simplification par plages

```sh
[START:END]
```

Les plages correspondent à toutes les valeurs comprises entre *START* et *END* incluses. Par exemple :

**192.168.[4:7].[0:255]** va correspondre à toutes les adresses IPv4 sur le réseau **192.168.4.0/22 (de 192.168.4.0 à 192.168.7.255)**.

**server[01:20].example.com** va correspondre à tous les hôtes nommés **server01.example.com** jusqu'à **server20.example.com**.

**[a:c].dns.example.com** va correspondre aux hôtes nommés **a.dns.example.com**,**b.dns.example.com** et **c.dns.example.com**.

**2001:db8::[a:f]** va correspondre à toutes les adresses IPv6 **de2001:db8::a** à **2001:db8::f**.

- Tester l'inventaire
  - Lister tous les hosts d'un groupe (exemple: canada)

    ```sh
    ansible canada --list-hosts
    ```
  
  - Présence d'une machine

    ```sh
    ansible washington1.example.com --list-hosts
    ```

- Emplacement par défaut de l'inventaire `/etc/ansible/hosts`, mais la bonne pratique dit qu'il ne faut pas utiliser ce répertoire
