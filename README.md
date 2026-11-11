# sentiment-app

Classifieur de sentiment d'avis clients en français (`positive` ou `negative`, avec une confiance), construit du
cadrage au paquet installable : modèle TF-IDF et régression logistique, API FastAPI `/v1` protégée par clé,
interface Streamlit, client web, service Java de démonstration, et un paquet Python publié en release.

Projet du module *Développement et déploiement d'applications intelligentes*, réalisé en solo par
Othmane Moussawi ([@OthmaneW37](https://github.com/OthmaneW37)).

## Installer le paquet (sans cloner le dépôt)

```powershell
pip install https://github.com/OthmaneW37/sentiment-app/releases/download/v1.0.0/sentiment_app-1.0.0-py3-none-any.whl
sentiment "Livraison rapide, très satisfait"
```

Le modèle est embarqué dans la roue. Détails et limites : [README-PAQUET.md](README-PAQUET.md) ;
versions : [CHANGELOG.md](CHANGELOG.md).

## Travailler sur le dépôt

```powershell
py -3.11 -m venv .venv                      # Python 3.12 accepté avec un avertissement
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt -r requirements-dev.txt
python src/make_demo.py                     # data/demo_reviews.csv : 290 avis de démonstration
python src/train.py                         # models/sentiment.joblib et models/metrics.json
Copy-Item .env.example .env                 # puis remplacer les deux clés (16 caractères minimum)
```

Générer une clé : `python -c "import secrets; print(secrets.token_urlsafe(24))"`. Dans `.env`,
`API_KEYS=<clé 1>:client,<clé 2>:admin` et `API_KEY=<clé 1>`. Le fichier `.env` est ignoré par Git.
Le modèle (`models/sentiment.joblib`) est reconstructible et n'est jamais commité ; seule sa mesure
(`models/metrics.json`) est versionnée.

### Lancer les applications (deux terminaux, `.venv` activé)

```powershell
uvicorn api.main:app --reload               # API : http://127.0.0.1:8000/docs (bouton Authorize)
streamlit run ui/app.py                     # interface : http://localhost:8501
python -m http.server 5500 --directory web  # client web : http://localhost:5500
```

### Vérifier

```powershell
python -m pytest -q --cov=api --cov-report=term-missing --cov-fail-under=95
ruff check .
mypy api
node --test web/client.test.mjs             # 12 tests du client web
```

Dernier résultat : **130 passed, 1 skipped, 4 xfailed**, couverture de `api/` à 100 %. Le test ignoré
(`tests/test_paquet.py`) vise le paquet installé ; il est exécuté et réussit dans le vérificateur du livrable.
Les 4 « xfail » sont des erreurs connues du jeu d'or, documentées et attendues (`xfail(strict=True)`).

## Construire et vérifier le livrable

```powershell
python -m build                             # dist/ : la roue et la distribution source
python scripts/verifier_livrable.py         # contrôle dans un environnement neuf : Bilan : CONFORME
gh release create v1.0.0 dist/* docs/livrable-tp4.txt --notes-file CHANGELOG.md
```

## Organisation

| Dossier | Contenu |
| --- | --- |
| `src/sentiment_app/` | le paquet publié : `predict`, commande `sentiment`, modèle embarqué (non versionné) |
| `src/` | scripts du modèle : `make_demo.py`, `train.py`, `evaluate.py`, `courbe_seuil.py`, `bilan_jeu_or.py` |
| `api/` | API FastAPI : routes `/v1`, clés et rôles, format d'erreur unique |
| `ui/` | interface Streamlit, cliente de l'API (`client.py`) |
| `web/`, `java/` | client web et service Spring Boot qui appellent l'API |
| `tests/` | tests : contrat, limites, jeu d'or, intégration, client, interface, paquet |
| `scripts/` | diagnostic de l'environnement, vérificateur du livrable |
| `docs/` | fiches de cadrage, analyses par semaine, journal, comptes rendus |

L'historique suit les étapes : cadrage, modèle, API, tests et qualité, interface, puis les trois derniers
commits « TP 2 » (API `/v1` protégée), « TP 3 » (interface sur `/v1`, graphiques) et « TP 4 » (paquet 1.0.0).
