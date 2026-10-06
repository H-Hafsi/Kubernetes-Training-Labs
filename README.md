# Kubernetes avec k3s : site de cours

Cours et travaux pratiques « Orchestration de conteneurs avec Kubernetes » du Master DevOps et Cloud Computing (ISET Tozeur).

Site publié : `https://ORGANISATION.github.io/kubernetes-k3s/` (à adapter dans `mkdocs.yml` et ici).

## Structure

```
docs/
├─ index.md                         syllabus (page d'accueil)
├─ partie-1-fondations/
│  ├─ index.md
│  ├─ ch01-architecture/            cours.md, lab-1-1-….md, lab-1-2-….md
│  └─ mini-projet-1.md
├─ partie-2-configuration-donnees-securite/
│  ├─ index.md
│  └─ mini-projet-2.md
├─ annexes/environnement-vmware.md
└─ assets/                          css/extra.css (charte), img/ (logo, bannière)
modeles/                            modèles de pages (non publiés)
```

## Ajouter une page

1. Copier le modèle voulu depuis `modeles/` dans le bon dossier (`chNN-theme/` pour un cours ou un lab).
2. Déclarer la page dans `nav` (`mkdocs.yml`).
3. Remplacer « À venir » par le lien dans `docs/index.md` et dans l'index de la Partie.

Nommage : `cours.md`, `lab-N-M-titre.md`, `mini-projet-N.md`.

## Prévisualiser et publier

```bash
pip install -r requirements.txt
mkdocs serve          # http://127.0.0.1:8000
```

Publication : pousser sur `main` ; dans `Settings`, `Pages`, choisir **Source : GitHub Actions**. La construction utilise `mkdocs build --strict`.

## À personnaliser

- Logo : remplacer `docs/assets/img/logo-iset.svg` (actuellement provisoire).
- Couleurs : variables au début de `docs/assets/css/extra.css`.
- Corrigés et notes enseignant : **ne pas les ajouter ici** (dépôt public), les garder dans un dépôt privé.
