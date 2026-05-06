# Guide CRM Pipeline — Zion Tour

## Objectif
Ajouter un pipeline de suivi commercial dans Google Sheets pour suivre chaque prospect de l'email jusqu'à la signature.

---

## 1. Structure des colonnes

Dans ta feuille `Contacts`, ajoute ces colonnes après la colonne C :

| A | B | C | D | E | F | G | H | I | J | K | L | M |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| email | prenom | structure | type_lieu | statut_pipeline | envoi_1 | envoi_2 | envoi_3 | clique | repondu | date_proposee | signe | notes |

### Détail des colonnes

| Colonne | Nom | Valeurs possibles |
|---|---|---|
| A | email | `programmateur@festival.be` |
| B | prenom | `Marc` |
| C | structure | `Festival de Namur` |
| D | type_lieu | `festival` / `salle` / `bar` / `culturel` |
| **E** | **statut_pipeline** | *(voir pipeline ci-dessous)* |
| F | envoi_1 | `envoye` / date |
| G | envoi_2 | `envoye` / date |
| H | envoi_3 | `envoye` / date |
| I | clique | `oui` / date |
| J | repondu | `oui` / `non` / date |
| K | date_proposee | `2026-06-15` |
| L | signe | `oui` / date |
| M | notes | Texte libre |

---

## 2. Pipeline de statuts (colonne E)

Ajoute cette validation des données sur la colonne E :

### Méthode :
1. Sélectionne toute la colonne E
2. Menu : **Données → Validation des données**
3. Règle : **Liste d'éléments**
4. Colle cette liste :

```
prospect, envoye, relance_1, relance_2, clique, repondu, interesse, date_proposee, signe, paye, refuse, froid
```

### Signification de chaque statut

| Statut | Signification | Action suivante |
|---|---|---|
| `prospect` | Contact importé, pas encore contacté | Attendre l'envoi automatique |
| `envoye` | Email initial envoyé | Attendre 7 jours → relance |
| `relance_1` | 1ère relance envoyée (J+7) | Attendre 7 jours → relance 2 |
| `relance_2` | 2ème relance envoyée (J+14) | Attendre 7 jours → passe en froid |
| `clique` | A cliqué sur le lien booking | Envoyer un email personnalisé |
| `repondu` | A répondu (même pour dire non) | Lancer la conversation |
| `interesse` | Demande plus d'infos | Envoyer le kit booking complet |
| `date_proposee` | Une date a été proposée | Négocier le cachet |
| `signe` | Contrat signé ✅ | Envoyer la facture |
| `paye` | Commission perçue 💰 | Clôturer |
| `refuse` | A décliné explicitement | Archiver |
| `froid` | 3 relances sans réponse | Mettre en pause 3 mois |

---

## 3. Mise en forme conditionnelle

Pour voir les priorités d'un coup d'œil :

1. Menu : **Format → Mise en forme conditionnelle**
2. Appliquer à la plage : `E:E`
3. Ajouter ces règles :

| Si le texte contient | Couleur de fond |
|---|---|
| `signe` ou `paye` | 🟢 Vert clair |
| `interesse` ou `date_proposee` | 🔵 Bleu clair |
| `clique` ou `repondu` | 🟡 Jaune clair |
| `relance_1` ou `relance_2` | 🟠 Orange clair |
| `prospect` ou `envoye` | ⚪ Blanc |
| `refuse` ou `froid` | 🔴 Rouge clair |

---

## 4. Script pour automatiser la mise à jour

Si tu veux que le statut se mette à jour automatiquement depuis l'Email Campaign Manager, ajoute ce script dans **Extensions → Apps Script** :

```javascript
/**
 * Met à jour le statut pipeline dans la sheet
 * depuis les logs de l'Email Campaign Manager (SQLite → CSV)
 */
function updatePipelineFromEmailEngine() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Contacts');
  const data = sheet.getDataRange().getValues();
  const headers = data[0];
  const emailCol = headers.indexOf('email');
  const statusCol = headers.indexOf('statut_pipeline');
  const envoi1Col = headers.indexOf('envoi_1');
  const envoi2Col = headers.indexOf('envoi_2');
  const envoi3Col = headers.indexOf('envoi_3');
  const cliqueCol = headers.indexOf('clique');

  // Exemple : importer depuis un fichier CSV exporté du module email
  // (à adapter selon ton format d'export)
  const csv = UrlFetchApp.fetch('URL_DE_TON_EXPORT_CSV').getContentText();
  const lines = csv.split('\n');

  for (let i = 1; i < lines.length; i++) {
    const cols = lines[i].split(',');
    const email = cols[0]?.trim().toLowerCase();
    if (!email) continue;

    // Trouver la ligne correspondante dans la sheet
    for (let j = 1; j < data.length; j++) {
      if (data[j][emailCol]?.toString().toLowerCase() === email) {
        // Mettre à jour selon le statut dans le CSV
        const csvStatus = cols[1]?.trim();
        if (csvStatus === 'sent') {
          sheet.getRange(j + 1, statusCol + 1).setValue('envoye');
          sheet.getRange(j + 1, envoi1Col + 1).setValue(new Date());
        } else if (csvStatus === 'clicked') {
          sheet.getRange(j + 1, statusCol + 1).setValue('clique');
          sheet.getRange(j + 1, cliqueCol + 1).setValue(new Date());
        }
        break;
      }
    }
  }
}
```

---

## 5. Vue tableau de bord (sheet séparée)

Crée une nouvelle feuille nommée `KPI` avec cette formule :

```
=NB.SI(Contacts!E:E;"prospect")
=NB.SI(Contacts!E:E;"envoye")
=NB.SI(Contacts!E:E;"relance_1")
=NB.SI(Contacts!E:E;"relance_2")
=NB.SI(Contacts!E:E;"clique")
=NB.SI(Contacts!E:E;"repondu")
=NB.SI(Contacts!E:E;"interesse")
=NB.SI(Contacts!E:E;"signe")
=NB.SI(Contacts!E:E;"paye")
=NB.SI(Contacts!E:E;"refuse")
=NB.SI(Contacts!E:E;"froid")
```

Cela te donne une vue instantanée de ton pipeline commercial.

---

## 6. Export depuis l'Email Campaign Manager

Pour synchroniser les statuts, tu peux exporter les données depuis l'API :

```bash
# Export des stats globales
curl http://localhost:5000/stats

# Export de tous les destinataires
curl http://localhost:5000/recipients

# Export filtré par statut
curl "http://localhost:5000/recipients?status=clicked"
```
