# M1-B1 — Pyrenex Crédit scoring

Réentraînement d'un modèle de scoring du risque de défaut pour Pyrenex Crédit.

Objectif : comparer le modèle historique `Pyrenex-risk-v1` entraîné en 2017 avec un modèle `Pyrenex-risk-v2` réentraîné sur le dataset Lending Club 2025 préparé.

## Reproduire

```bash
pip install -r requirements.txt
python src/train.py --config balanced
python src/evaluate.py --update-meta
```

## Modèle retenu

Configuration retenue : `RandomForestClassifier` avec `class_weight="balanced"`.

Hyperparamètres principaux :

- `n_estimators=200`
- `max_depth=10`
- `min_samples_leaf=10`
- `class_weight="balanced"`
- `random_state=42`

## Métriques finales

| Modèle | Dataset | F1 macro | ROC-AUC | Recall défaut |
|---|---|---:|---:|---:|
| Pyrenex-risk-v1 | Test split 2017 | 0.5018 | 0.7296 | 0.0500 |
| Pyrenex-risk-v2 balanced | Holdout 2025 | 0.6130 | 0.7371 | 0.6455 |

## Verdict

Décision : remplacer `Pyrenex-risk-v1` par `Pyrenex-risk-v2`, avec validation métier avant mise en production complète.

Voir [`verdict.md`](verdict.md).

## Fichiers principaux

- `notebooks/M1-B1_fernando_scoring.ipynb` : EDA, entraînement, comparaison
- `src/preprocess.py` : preprocessing reproductible
- `src/train.py` : entraînement RandomForest
- `src/evaluate.py` : évaluation holdout
- `models/pyrenex_risk_v2.joblib` : Pipeline complet persisté
- `models/pyrenex_risk_v2.json` : métadonnées modèle
- `contract_test.py` : validation du modèle persisté
- `experiments.md` : traçage des runs
- `verdict.md` : décision finale