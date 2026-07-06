# Experiments — Pyrenex Crédit scoring v2

Règle de comparabilité : les scores ci-dessous sont calculés uniquement sur un split interne de `lending_club_train.csv` avec `test_size=0.2`, `stratify=y` et `random_state=42`.

Aucun score holdout n'est utilisé dans ces runs intermédiaires.

## Baseline 2017 — référence historique

| Modèle | Dataset | F1 macro | ROC-AUC | Note |
|---|---|---:|---:|---|
| Pyrenex-risk-v1 | 2017 test split | 0.5018 | 0.7296 | RandomForest historique, sans stratify, sans class_weight |

## Runs v2 — validation interne 2025

| Run | Modèle | Hyperparamètres principaux | F1 macro interne | ROC-AUC interne | Décision |
|---|---|---|---:|---:|---|
| A | RandomForest default | `n_estimators=100`, `random_state=42`, `n_jobs=-1` | 0.5063 | 0.7190 | Rejeté |
| B | RandomForest balanced | `n_estimators=200`, `max_depth=10`, `min_samples_leaf=10`, `class_weight="balanced"`, `random_state=42`, `n_jobs=-1` | 0.6104 | 0.7440 | Retenu |

## Choix retenu

Le run B est retenu car il améliore nettement le F1 macro sur le split interne, tout en améliorant aussi le ROC-AUC. Le paramètre `class_weight="balanced"` est cohérent avec le problème de classe minoritaire observé dans la baseline 2017, où le recall de `Charged Off` était très faible.
