# Verdict — Pyrenex-risk-v2

## Décision

Je recommande de remplacer `Pyrenex-risk-v1` par `Pyrenex-risk-v2`, avec une phase de validation métier avant mise en production complète.

## Comparaison chiffrée

| Modèle | Dataset d'évaluation | F1 macro | ROC-AUC | Recall défaut |
|---|---|---:|---:|---:|
| Pyrenex-risk-v1 | Test split 2017 | 0.5018 | 0.7296 | 0.0500 |
| Pyrenex-risk-v2 balanced | Holdout 2025 | 0.6130 | 0.7371 | 0.6455 |

Le modèle v2 améliore nettement le F1 macro, qui passe de 0.5018 à 0.6130. Le ROC-AUC progresse légèrement, de 0.7296 à 0.7371. Le gain le plus important concerne la détection des défauts : le recall de la classe `Charged Off` passe de 0.05 à 0.6455.

## Matrice de confusion v2 sur holdout

|  | Prédit remboursé | Prédit défaut |
|---|---:|---:|
| Vrai remboursé | 3449 | 1448 |
| Vrai défaut | 391 | 712 |

Le modèle v2 détecte 712 défauts sur 1103, contre une baseline historique qui détectait seulement environ 5% des défauts. En contrepartie, il génère davantage de faux positifs : 1448 dossiers remboursés sont classés comme défaut potentiel.

## Justification du choix

Le run retenu est la configuration `balanced` du `RandomForestClassifier` :

- `n_estimators=200`
- `max_depth=10`
- `min_samples_leaf=10`
- `class_weight="balanced"`
- `random_state=42`

Cette configuration est cohérente avec le problème principal identifié dans la baseline : la classe minoritaire `Charged Off` était très mal détectée. Le paramètre `class_weight="balanced"` permet de donner plus de poids à cette classe pendant l'entraînement.

## Précautions avant mise en production

Avant une mise en production complète, je recommande :

1. Valider avec le métier le coût des faux positifs, car le modèle refuse ou alerte davantage de dossiers.
2. Surveiller les métriques par classe en production, pas seulement l'accuracy.
3. Auditer les variables sensibles ou quasi-sensibles comme `fico_range_low`, `annual_inc`, `emp_length` et `home_ownership`.
4. Ajouter un suivi de dérive des données et des performances dans la future CI/CD.
5. Conserver le modèle v1 comme rollback possible pendant la phase de transition.

Conclusion : `Pyrenex-risk-v2` est meilleur pour l'objectif métier principal — détecter les défauts — mais son seuil de décision et son impact sur les faux positifs doivent être validés avant déploiement.
