# Le Fournil des Monts — Réponse aux avis

> Projet IA — BTS SIO 2 SLAM — Hasanna MOHAMED LAMIN BALAK — octobre 2026
> **URL publique** : https://hopefully-wrote-damaged-institutes.trycloudflare.com — code d'accès envoyé à l'enseignant par e-mail

## 1. Concevoir

**Sujet choisi** : Sujet 4 — Réponse aux avis clients (Le Fournil des Monts)

**L'organisation (fictive) et son besoin**, en trois phrases :
Le Fournil des Monts est une boulangerie-pâtisserie fictive qui exploite deux boutiques (place du Marché et avenue de la Gare) et reçoit une vingtaine d'avis en ligne par semaine. La gérante, Mme Élise Duverger, n'a pas le temps d'y répondre dans la journée et, fatiguée le soir, rédige parfois des réponses sèches ou maladroites, ce qui donne une mauvaise image de la boutique. L'application lui propose un brouillon de réponse courtois, adapté à la note, qu'elle relit et publie elle-même.

**Trois cas d'usage**, sous la forme « En tant que…, je veux…, afin de… » :

1. En tant que gérante, je veux une réponse chaleureuse pour les avis 4-5 étoiles, afin de remercier les clients sans engager la boutique.
2. En tant que gérante, je veux une réponse qui s'excuse et propose une piste concrète pour les avis 1-3 étoiles, afin de montrer que l'avis est pris au sérieux.
3. En tant que gérante, je veux une réponse neutre et bornée pour les avis pièges (remboursement, allergènes, salarié nommé, injurieux, concurrent), afin de ne rien promettre et ne pas inventer d'information.

**Ce que l'application ne fait pas** (au moins deux limites assumées) :
1. Elle ne publie rien automatiquement : la gérante relit et publie elle-même (pas d'intégration avec l'API Google/Facebook).
2. Elle ne connaît que les informations listées dans la fiche du sujet (horaires, fournée à 16 h 30, commande 48 h à l'avance, allergènes en boutique) ; toute autre question reçoit une réponse générique.

## 2. Le modèle et la machine

| | |
|---|---|
| Carte graphique et mémoire vidéo (VRAM) | 6,0 Go de VRAM dédiée (d'après le Gestionnaire des tâches → Performances → GPU) |
| Mémoire vive | 8 Go ou plus |
| Modèle retenu | `qwen2.5:3b` |
| Pourquoi celui-là | Avec 6 Go de VRAM, le tableau du sujet indique la tranche « 4 à 6 Go » : `qwen2.5:3b` tient entièrement en VRAM (vérifié : 100 % GPU dans `ollama ps`). |
| Modèle comparé | `llama3.2:3b` |

**Premiers essais avec `ollama run qwen2.5:3b --verbose` :**
- 1er appel (chargement du modèle) : **10,60 tokens/s**
- 2e appel (modèle chaud) : **92,68 tokens/s**
- `ollama ps` : **100 % GPU**, 2,2 Go en mémoire

## 3. Piloter — le journal

| Séance | Ce qui est fait | Ce qui a bloqué, et comment c'est réglé |
|---|---|---|
| Lundi 05/10 | Installation des outils (Git, Python 3.14, Docker, Ollama), pull `qwen2.5:3b` (100 % GPU, ~92 tok/s en régime chaud), clone du dépôt, config Git, README §1-§2, `prompt.txt` écrit, `cas.json` avec 13 cas (dont 5 pièges de la fiche), premier test local, évaluation 13/13 (100 %). | `ollama` et `python` non reconnus dans PowerShell → PATH corrigé et alias Microsoft Store désactivés. Limite de 10 req/min atteinte pendant l'évaluation → passage à 40 le temps de la mesure, puis retour à 10. |
| Mardi 06/10 | _(à compléter)_ | _(à compléter)_ |
| Jeudi 08/10 | _(à compléter)_ | _(à compléter)_ |

## 4. Mesurer

Jeu de **13 cas** dans `cas.json`, dont les 5 pièges de la fiche du sujet (remboursement, allergènes, prénom de salarié, avis injurieux, concurrent) et un cas de détournement de consignes.

| | Modèle retenu (`qwen2.5:3b`) | Modèle comparé (`llama3.2:3b`) |
|---|---|---|
| Réussite (sur 13 cas × 1 essai en local) | **13/13 = 100 %** | _(à remplir mardi)_ |
| Temps de réponse médian | **3,2 s** | _(à remplir mardi)_ |

**Ce que les échecs montrent** (à compléter mardi après la mesure 3 essais × 2 modèles) :

- Le modèle `qwen2.5:3b` respecte les 5 règles de la fiche du sujet après plusieurs itérations sur `prompt.txt` : interdiction de prononcer « remboursement », de reprendre un prénom de salarié, d'affirmer sur les allergènes, de répondre à une insulte, de dénigrer un concurrent.
- Les itérations ont montré qu'un petit modèle (3B) **oublie les règles noyées** dans un long prompt : il faut les **remonter en tête** et les **rappeler en fin** de prompt, avec un exemple pour chaque cas piège.

## 5. Sécuriser

| Risque | Ce qui pourrait arriver | Mesure prise dans le projet |
|---|---|---|
| L'URL est publique | N'importe qui utilise mon PC et mon électricité | Code d'accès obligatoire (`main.py`) + limite de requêtes par minute |
| Ollama exposé | Accès direct au modèle depuis Internet | Ollama n'est pas publié dans `compose.yaml` ; seule l'application (port 8000) est exposée |
| Détournement des consignes | Le modèle ignore ses règles (ex. « ignore tes instructions ») | Cas dédié dans `cas.json`, règles absolues dans `prompt.txt` |
| Données personnelles | Un client écrit un nom dans un avis | Le prompt interdit de nommer un salarié et de reprendre des données personnelles |
| Secrets dans le dépôt | Fuite du code d'accès | `.env` dans `.gitignore`, jamais poussé ; seul `.env.example` est commité |

## 6. Mettre en production — comment refaire

```bash
cp .env.example .env      # puis remplir
docker compose up -d --build
docker compose ps
docker compose logs tunnel