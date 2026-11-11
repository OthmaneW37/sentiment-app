# sentiment-app

Classe un avis client rédigé en français : `positive` ou `negative`, avec une confiance.

## Installation

    pip install sentiment_app-1.0.0-py3-none-any.whl

Le fichier se télécharge sur la page de la release GitHub. Le modèle est embarqué dans la roue :
aucun autre fichier n'est nécessaire. Python 3.11 ou plus récent ; scikit-learn 1.5 est installé avec le paquet.

## En ligne de commande

    sentiment "Livraison rapide, très satisfait"
    sentiment --json "Produit cassé et service injoignable, à fuir"

La commande affiche le verdict et sa confiance (ou un objet JSON avec `--json`) et renvoie le code 0.
Un texte vide donne un message d'erreur et le code 2.

## En Python

    from sentiment_app import predict
    predict("Colis abîmé")   # {'label': 'negative', 'score': ...}

## Limites

Modèle de démonstration entraîné sur 290 avis : F1 macro 0,879 sur 58 avis de test. Il est sensible aux
avis mitigés (« bon puis mauvais ») : un sac de mots ne voit pas l'ordre des phrases. Le paquet répond
toujours positive ou negative, sans seuil d'abstention : ne pas l'utiliser pour décider seul. L'API du
dépôt, elle, répond INDETERMINE sous le seuil de confiance de 0,60.
