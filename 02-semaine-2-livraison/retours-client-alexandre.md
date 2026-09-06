# Retours client à intégrer — Alexandre

Ce document liste les demandes client connues à reprendre en début de semaine 2. Il doit être complété après la reprise collective et transformé en exigences validables.

## 1. RGPD et durée de conservation

### Demande exprimée

Alexandre a évoqué la prise en compte du RGPD, avec une hypothèse de conservation des données pendant deux ans.

### Points à clarifier

- Quelles données sont concernées ?
- La durée de deux ans concerne-t-elle toutes les données ou seulement certaines catégories ?
- Que fait-on à l'échéance : suppression, anonymisation, archivage, demande de renouvellement ?
- Faut-il prévoir un email d'information ou de renouvellement de consentement ?
- Qui est responsable de la décision finale côté club ?

### Impacts projet

- champ de date de collecte ou dernière interaction ;
- statut de conservation ;
- traitement de purge ou d'anonymisation ;
- documentation RGPD ;
- règles d'exclusion des campagnes.

## 2. Export complet des contacts d'un événement

### Demande exprimée

Le club souhaite pouvoir exporter l'intégralité des contacts liés à un événement, par exemple les clients présents ou inscrits lors d'un match.

### Points à clarifier

- L'export concerne-t-il les acheteurs, les participants, les invités ou tous les contacts liés à l'événement ?
- Quelles colonnes doivent être exportées ?
- Le consentement doit-il filtrer l'export ou uniquement l'envoi Brevo ?
- Faut-il proposer un export CSV et/ou une synchronisation directe Brevo ?

### Impacts projet

- écran événement ;
- bouton export ;
- route API d'export ;
- requête SQL ;
- test end-to-end ;
- documentation utilisateur.

## 3. Invitations et tickets offerts

### Demande exprimée

Le club veut retrouver les invitations, notamment les tickets offerts à chaque match, et savoir si les personnes invitées sont venues ou non lorsque l'information est disponible.

### Points à clarifier

- Comment les invitations sont-elles identifiées dans les données Weezevent ?
- Le champ tarif, code promo, type de billet ou montant permet-il de les reconnaître ?
- La présence réelle est-elle disponible via les scans ou seulement l'inscription ?
- Comment représenter une invitation non utilisée ?

### Impacts projet

- modèle de données ;
- champ `ticket_type` ou équivalent ;
- statut invitation ;
- statut présence ;
- indicateurs ;
- filtres ;
- export spécifique.

## 4. Consentement et désinscription

### Demande exprimée

Si un contact ne veut pas recevoir les emails du club, il doit être désinscrit grâce au consentement et exclu des envois.

### Points à clarifier

- Quelle est la source de vérité du consentement : Brevo, Weezevent, dashboard, formulaire ?
- Que se passe-t-il si Brevo indique une désinscription mais que Weezevent réimporte le contact ?
- Faut-il un statut `désinscrit`, `consentement refusé`, `consentement inconnu` ?
- Qui peut modifier ce statut ?

### Impacts projet

- modèle de données ;
- règle métier ;
- écran contact ;
- export Brevo ;
- synchronisation ;
- tests ;
- documentation RGPD.

## 5. Synthèse prioritaire

| Demande | Priorité proposée | Pôle principal | Preuve attendue |
|---|---|---|---|
| Gestion du consentement | Must | Développement / données | Contact désinscrit exclu d'un envoi Brevo |
| Export contacts d'un événement | Must | Développement | CSV ou synchronisation validée |
| Invitations | Must si données disponibles | Données / développement | Liste des invitations d'un événement |
| Présence réelle | Should selon données | Données / développement | Distinction présent / absent |
| Conservation deux ans | Should | Données / RGPD | règle documentée + champ ou procédure |
| Email avant suppression | Could | Développement / RGPD | scénario décrit ou prototype |
