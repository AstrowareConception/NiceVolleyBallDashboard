# Checklist de livraison

À compléter avant la présentation client.

## Fonctionnel

- [ ] Synchronisation Weezevent opérationnelle.
- [ ] Liste des événements consultable.
- [ ] Export complet des contacts d'un événement opérationnel.
- [ ] Nouveaux spectateurs identifiables.
- [ ] Invitations identifiables si les données le permettent.
- [ ] Présence réelle prise en compte si les données le permettent.
- [ ] Consentement visible ou exploitable.
- [ ] Désinscription prise en compte.
- [ ] Contacts désinscrits exclus de Brevo.
- [ ] Bilan de synchronisation affiché.
- [ ] Messages d'erreur compréhensibles.

## Technique

- [ ] Environnement de production ou préproduction disponible.
- [ ] Docker Compose de production prêt.
- [ ] Reverse proxy configuré.
- [ ] HTTPS configuré ou réserve explicite.
- [ ] Variables d'environnement configurées hors Git.
- [ ] Base non exposée publiquement.
- [ ] Logs accessibles.
- [ ] Pipeline automatique ou semi-automatique documenté.

## Données et RGPD

- [ ] Données nécessaires uniquement.
- [ ] Règle de conservation documentée.
- [ ] Source du consentement indiquée.
- [ ] Désinscription testée.
- [ ] Exports limités aux champs utiles.
- [ ] Captures anonymisées si besoin.

## Sauvegarde et reprise

- [ ] Script de sauvegarde PostgreSQL disponible.
- [ ] Sauvegarde testée.
- [ ] Procédure de restauration écrite.
- [ ] Restauration testée ou réserve documentée.
- [ ] Emplacement des sauvegardes indiqué.

## Documentation

- [ ] Guide utilisateur relu.
- [ ] Guide exploitation relu.
- [ ] Procédure de déploiement relue.
- [ ] PV de recette prêt.
- [ ] Backlog restant à jour.
- [ ] Limites connues écrites.

## Remise client

- [ ] Accès remis au client ou procédure de remise définie.
- [ ] Comptes étudiants retirés ou temporaires identifiés.
- [ ] Propriétaire du VPS confirmé.
- [ ] Propriétaire des clés API confirmé.
- [ ] Validation ou réserves client consignées.
