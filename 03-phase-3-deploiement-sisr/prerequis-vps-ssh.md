# Prérequis VPS et accès SSH

## Informations à obtenir

- fournisseur du VPS ;
- propriétaire du compte ;
- système d'exploitation ;
- adresse IP ;
- nom de domaine ou sous-domaine ;
- accès SSH ;
- utilisateur d'administration ;
- politique de sauvegarde fournisseur ;
- ports ouverts.

## Bonnes pratiques SSH

- privilégier l'authentification par clé ;
- éviter le partage de mot de passe ;
- créer des comptes nominatifs si possible ;
- limiter les droits ;
- documenter les accès remis au client.

## Ports

- 22 : SSH ;
- 80 : HTTP ;
- 443 : HTTPS ;
- port applicatif interne non exposé ;
- PostgreSQL non exposé publiquement.
