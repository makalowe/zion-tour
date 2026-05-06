# 00 - Contexte Global Zion Tour

## Identite du projet

- Nom: Zion Tour
- Type: Agence de booking artistes
- Mission: Trouver des dates (festivals, salles, evenements) pour les artistes representes
- Modele economique: Commission sur les dates signees

## Modele de revenus

- Commission cible: 15% a 20% du cachet artiste
- Formule:
  - CA agence = Nombre de dates signees x Cachet moyen x Taux de commission
- Exemple:
  - 10 dates x 2 000 EUR x 20% = 4 000 EUR

## Cible commerciale

- Programmateurs festivals
- Salles de concert
- Lieux culturels
- Tourneurs / organisateurs d'evenements

## Positionnement artistique

- Focus: Reggae / Afro-Funk / univers afro-caribeen live
- Cas actif fort: Princess Erika & Grand Chacal Bar
- Objectif: Obtenir des dates 2026 en prospection structuree

## Systeme technique en place (automation booking)

- Source contacts: Google Sheets
- Colonnes:
  - A = email
  - B = statut
  - C = clic
- Envoi: SMTP Hostinger
- Adresse expediteur: booking@zionworld.fr
- Cadence actuelle:
  - 1 email toutes les 5 minutes
  - moins de 90 emails par jour
- Statut d'envoi:
  - ecrit `envoye` en colonne B apres succes
- Tracking clic:
  - lien `Infos booking` unique par contact
  - ecrit `clic` en colonne C au clic
- Image:
  - image inline dans le corps du mail (si chemin configure)

## Fichiers techniques de reference (workspace code)

- app.py
- tracking_server.py
- send_hourly.ps1
- .env
- README.md

## Parametres clefs `.env`

- GOOGLE_SHEET_ID
- GOOGLE_SHEET_RANGE=Contacts!A:C
- EMAIL_SUBJECT
- EMAIL_BODY_TEMPLATE
- EMAIL_INLINE_IMAGE_PATH
- BOOKING_URL=https://chacalbarband.wixsite.com/princesserika
- TRACKING_BASE_URL
- SMTP_HOST=smtp.hostinger.com
- SMTP_PORT=465
- SMTP_USERNAME
- SMTP_PASSWORD
- SMTP_USE_SSL=true
- MAX_EMAILS_PER_WINDOW=1
- SEND_WINDOW_MINUTES=5
- MAX_EMAILS_PER_DAY=90
- STATUS_SENT_VALUE=envoye
- CLICKED_VALUE=clic

## Delivrabilite email (etat)

- SPF: configure
- DMARC: configure
- DKIM: configure
- Point de vigilance:
  - reputation domaine
  - qualite des listes
  - qualite des messages
  - volume progressif

## Etat operationnel actuel

- Automatisation fonctionnelle
- Envois reels realises
- Suivi statut dans sheet operationnel
- Lien booking operationnel
- Tracking clic pret, mais necessite une URL publique pour usage externe fiable

## Risques / limites

- `TRACKING_BASE_URL=http://localhost:8000` ne fonctionne pas pour des destinataires externes
- Le volume d'envoi ne remplace pas la qualite commerciale (ciblage + relance)
- Sans suivi KPI hebdo, risque de travail sans progression mesurable

## KPI hebdomadaires a suivre

- Emails envoyes
- Taux de reponse
- Taux de clic
- Nombre de conversations ouvertes
- Nombre de dates proposees
- Nombre de dates signees
- CA agence (commission)

## Priorites business (court terme)

1. Qualifier base prospects (qualite avant volume)
2. Stabiliser sequence commerciale (mail initial + relance 1 + relance 2)
3. Produire un kit booking propre (bio, videos live, fiche technique, conditions)
4. Standardiser process de nego + contrat
5. Piloter chaque semaine avec chiffres

## Prochaines actions conseillees

1. Mettre une URL publique de tracking (domaine/sous-domaine)
2. Ajouter un CRM simple dans la sheet (repondu, interesse, date proposee, signe)
3. Segmenter les campagnes par type de lieu
4. A/B tester 2 objets email et 2 corps de message

## Notes governance

- Ce document est la memoire centrale (second cerveau) du projet
- Toute decision importante doit etre ajoutee ici
- Toute modification de stack/process doit etre tracee avec date

## Journal de mise a jour

- 2026-04-23: Creation du contexte global centralise
