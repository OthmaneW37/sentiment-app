# Journal des versions

## [1.0.0] - 2026-11-11

### Ajouté
- Paquet installable `sentiment-app` : fonction `predict(texte)` et commande `sentiment`.
- Modèle TF-IDF et régression logistique embarqué dans la roue (`sentiment_app/modele/sentiment.joblib`).
- API FastAPI `/v1` protégée par clé (`X-API-Key`, rôles client et admin), format d'erreur unique,
  ancienne route `/predict` dépréciée.
- Client web sans dépendance et service Java de démonstration qui consomment l'API.
- Interface Streamlit cliente de l'API, avec deux graphiques (répartition des verdicts, distribution de la confiance).
- Script `scripts/verifier_livrable.py` : contrôle de la roue dans un environnement neuf.

### Limites connues
- Modèle de démonstration entraîné sur 290 avis ; il se trompe surtout sur les avis mitigés.
- Le paquet répond toujours positive ou negative : pas de seuil d'abstention (présent dans l'API seulement).
- Le modèle sérialisé dépend de scikit-learn 1.5 ; une autre version majeure peut le refuser.
