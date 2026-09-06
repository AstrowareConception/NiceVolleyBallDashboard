# Contexte client et besoin métier

## Organisation support

Le projet est réalisé pour le **Nice Volley Ball**, club professionnel qui souhaite mieux exploiter ses données spectateurs, billetterie et communication.

Le besoin a été formulé autour d'un objectif simple : disposer d'un outil interne permettant de mieux connaître les publics du club et d'améliorer la relation avec les supporters, visiteurs, invités et prospects.

## Outils existants

Les outils identifiés dans le projet sont notamment :

- **Weezevent / WeezTicket** pour la billetterie ;
- les données d'invitations et de présence lorsqu'elles sont disponibles ;
- **Brevo** pour les contacts, newsletters et campagnes ;
- des fichiers internes de suivi ;
- des traitements manuels réalisés par l'équipe du club.

## Difficultés constatées

Les données sont utiles mais dispersées. Le club peut avoir des informations dans Weezevent, d'autres dans Brevo et d'autres dans des exports ou fichiers internes. Cette dispersion rend difficile :

- le suivi d'un spectateur dans le temps ;
- l'identification des nouveaux spectateurs ;
- l'identification des spectateurs réguliers ;
- l'exploitation des invitations ;
- la préparation de campagnes ciblées ;
- le respect fin du consentement ;
- le contrôle des désinscriptions ;
- l'analyse de la fréquentation événement par événement.

## Objectifs métier

Le dashboard doit aider le club à :

- mieux connaître les contacts liés aux événements ;
- exporter tous les contacts associés à un match ou événement ;
- identifier les nouveaux spectateurs venus au dernier événement ;
- distinguer les invitations et, si possible, les invitations utilisées ou non ;
- préparer des listes de contacts pertinentes pour Brevo ;
- éviter d'envoyer des emails à des contacts désinscrits ;
- conserver une trace des synchronisations ;
- disposer d'un outil simple, exploitable et documenté.

## Contraintes fortes

- L'outil doit rester simple et utile.
- Les données personnelles doivent être traitées avec prudence.
- La conservation des données doit être clarifiée, avec une hypothèse de deux ans évoquée par le client.
- Les clés API ne doivent pas être exposées.
- L'environnement final doit être transférable au client.
- Les étudiants doivent produire des livrables professionnels et défendables en BTS SIO.

## Point de vigilance RGPD

Les informations manipulées peuvent contenir des données personnelles : nom, prénom, email, téléphone, historique de présence, billets, invitations, consentement marketing. Le projet doit donc intégrer dès la conception :

- la minimisation des données ;
- le consentement ;
- la désinscription ;
- la limitation de conservation ;
- la sécurisation des accès ;
- la traçabilité des traitements ;
- la prudence dans les captures et jeux de démonstration.
