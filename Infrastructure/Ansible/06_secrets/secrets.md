# Gestion des secrets

## Création d'un fichier chiffré

- Création simple par saisie de mot de passe

```sh
$ ansible-vault create secret.yml
New Vault password: mdp
Confirm New Vault password: mdp
```

- Création utilisant un fichier de mot de passe.
Il est à notre charge de protéger ce fichier de mdp

```sh
ansible-vault create --vault-password-file=vault-pass secret.yml
```

## Visionner un fichier chiffré

```sh
$ansible-vault view secret1.yml
Vault password: mdp
```

## Modification d'un fichier chiffré

```sh
$ansible-vault edit secret.yml
Vault password: mdp
```

## Chiffrer un fichier existant

```sh
$ansible-vault encrypt secret1.yml secret2.yml
New Vault password: mdp
Confirm New Vault password: mdp
Encryption successful
```

## Déchiffrer un fichier existant

```sh
$ansible-vault decrypt secret1.yml --output=secret1-decrypted.yml
Vault password: mdp
```

## Modification du Mot de passe d'un fichier chiffré

- Simple

```sh
$ansible-vault rekey secret.yml
Vault password: mdp
New Vault password: mdp2
Confirm New Vault password: mdp2
Rekey successful
```

- Par fichier

```sh
ansible-vault rekey --new-vault-password-file=NEW_VAULT_PASSWORD_FILE secret.yml
```

## Playbook utilisant des fichiers chiffrer

Il est nécessaire de fournir le mot de passe au playbook:

- simple

```sh
$ansible-playbook --vault-id @prompt site.yml
Vault password (default): mdp
```

- Par fichier

```sh
ansible-playbook --vault-password-file=vault-pw-file site.yml
```
