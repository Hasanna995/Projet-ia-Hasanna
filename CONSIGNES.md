# Projet IA à domicile — BTS SIO 2 SLAM

**Les consignes complètes** (sujets, exigences, déroulé du lundi et du mardi, barème) :
<https://ggaillard.github.io/portail-bts/distance/projet-ia.html>

Séances : **lundi 5 et mardi 6 octobre 2026, de 15 h à 17 h**. Questions et rendu : par e-mail, à l'adresse Gmail habituelle de l'enseignant, objet `[BTS2 Projet IA] NOM Prénom — …`.

## Démarrer en trois gestes

1. En haut de cette page : **Use this template → Create a new repository**, à votre nom, nommé `projet-ia-<prenom>`.
2. Clonez **votre** dépôt, puis `cp .env.example .env` (Windows : `copy .env.example .env`) et remplissez `.env`. Ce fichier ne se commite jamais.
3. Remplissez `README.md` au fil des deux séances : c'est votre dossier.

| Fichier | Rôle |
|---|---|
| `main.py` | l'application FastAPI : page, `/api/demander`, `/sante`, code d'accès, limite de requêtes |
| `prompt.txt` | la consigne donnée au modèle — à écrire pour votre sujet |
| `static/index.html` | la page — à adapter |
| `Dockerfile`, `compose.yaml` | la mise en production : l'application et le tunnel vers l'URL publique |
| `.env.example` | les réglages, à copier en `.env` |
| `evaluer.py`, `cas.json` | la mesure : rejouer vos cas et compter |
| `README.md` | votre dossier |
| `sujets/` | les six sujets détaillés, avec leurs données d'exemple — [commencez ici](sujets/) |

*Organisations et données des projets : fictives uniquement.*
