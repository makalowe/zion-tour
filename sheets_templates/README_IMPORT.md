# Import Google Sheets - Zion Tour

## Fichiers

- Artists.csv
- Prospects.csv
- Campaigns.csv
- Interactions.csv
- Templates.csv
- Inboxes.csv

## Import rapide

1. Ouvrir un nouveau Google Sheets.
2. Renommer/supprimer `Feuille 1`.
3. Pour chaque fichier CSV:
   - `Fichier > Importer`
   - Onglet `Televerser`
   - Selectionner le CSV
   - Type d'import: `Inserer une nouvelle feuille`
4. Renommer chaque onglet avec le meme nom que le fichier:
   - Artists
   - Prospects
   - Campaigns
   - Interactions
   - Templates
   - Inboxes

## Regle de pilotage

- `Prospects.status` est le statut business principal.
- `Interactions` garde l'historique de chaque evenement.
- `Campaigns` sert au suivi KPI global par campagne.
