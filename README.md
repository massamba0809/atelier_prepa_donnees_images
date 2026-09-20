# Atelier Préparation de Données Images

Nettoyage et préparation d'un dataset de tri de déchets (`cardboard`, `glass`, `metal`, `paper`, `plastic`, `trash`) en vue de l'entraînement d'un modèle de Machine Learning / Deep Learning.

## Contexte

Les images collectées proviennent de plusieurs sources et présentent des problèmes classiques d'un dataset brut : dimensions et formats hétérogènes, images grayscale/RGB/RGBA mélangées, images trop petites, corrompues, vides, dupliquées, mal classées, et classes déséquilibrées. L'objectif de l'atelier est de construire, à partir de ces données brutes, un jeu de données propre et homogène, prêt pour l'entraînement.

## Structure du projet

```
atelier_prepa_donnees_images/
├── README.md
├── .gitignore
├── notebooks/
│   └── atelier_prepa_donnees_images.ipynb   # notebook principal, toutes les parties
├── reports/
│   └── audit_images.csv                     # résultats de l'audit du dataset
└── data/
    ├── raw/           # dataset original, lecture seule, jamais modifié
    │   ├── cardboard/
    │   ├── glass/
    │   ├── metal/
    │   ├── paper/
    │   ├── plastic/
    │   └── trash/
    └── cleaned/       # dataset nettoyé, généré par le notebook
        ├── train/<classe>/
        ├── val/<classe>/
        └── test/<classe>/
```

`data/raw/` ne doit jamais être modifié : c'est la référence brute. Toutes les transformations produisent leur résultat dans `data/cleaned/`.

## Résumé des étapes réalisées

| Partie | Contenu | Résultat clé |
|---|---|---|
| 1 | Exploration du dataset | 1032 images, métadonnées extraites (nom, classe, format, mode, dimensions, écart-type, canaux, poids) |
| 2 | Détection des images corrompues | 6 images corrompues |
| 3 | Détection des images vides | 4 images (noires/blanches uniformes) |
| 4 | Différences de résolution | 4 résolutions distinctes, dominée par 512×384 ; 13 images < 64×64 |
| 5 | Différences de canaux | 1006 RGB, 18 RGBA, 2 palette (P) |
| 6 | Détection des doublons | 15 groupes de doublons (30 fichiers), dont des doublons inter-classes |
| 7 | Images mal classées | Contrôle visuel + croisement avec les doublons inter-classes ; 2 erreurs de classement confirmées et corrigées via la déduplication |
| 8 | Déséquilibre des classes | `trash` sous-représentée (49 images vs 251 pour `paper`), ratio 5.12 |
| 9 | Redimensionnement | Toutes les images ramenées à 224×224 (ratio conservé + padding), 996 images conservées |
| 10 | Uniformisation des canaux | 100% des images en RGB |
| 11 | Mise à l'échelle des pixels | Normalisation à la volée `[0, 255] → [0, 1]` (non stockée sur disque) |
| 12 | Découpage Train/Val/Test | 70% / 15% / 15%, stratifié par classe (697 / 149 / 150 images) |
| 13 | Data Augmentation | Classe `trash` du train augmentée de 34 → 132 images (Keras `ImageDataGenerator`), appliquée uniquement après le découpage pour éviter la fuite de données |

## Comment exécuter le notebook

```bash
pip install pillow numpy pandas matplotlib scikit-learn tensorflow-cpu jupyter nbconvert
jupyter notebook notebooks/atelier_prepa_donnees_images.ipynb
```

Le notebook s'exécute de haut en bas sans intervention manuelle, dans l'ordre des parties de l'énoncé.

## Auteur

Massamba Fall Séne
