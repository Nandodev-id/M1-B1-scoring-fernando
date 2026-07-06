# Modèle Pyrenex-risk-v2

Ce dossier contient le modèle final retenu :

- `pyrenex_risk_v2.joblib` : Pipeline scikit-learn complet
- `pyrenex_risk_v2.json` : métadonnées, hyperparamètres et métriques holdout

## Recharger le modèle

```python
from pathlib import Path

import joblib
import pandas as pd

model = joblib.load(Path("models") / "pyrenex_risk_v2.joblib")
df = pd.read_csv(Path("data") / "lending_club_holdout.csv").drop(columns=["loan_status"])

predictions = model.predict(df.head(3))
probabilities = model.predict_proba(df.head(3))
```

## Vérifier le contrat

```bash
python contract_test.py
```