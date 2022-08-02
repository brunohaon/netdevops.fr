---
title:  "PySnooper – Décorateur Débugeur"
categories: "Librairies Python"
---

## PySnooper – Décorateur Débugeur

PySnooper permet de faire du Debug sur une fonction Python juste en ajoutant le décorateur @pysnooper.snoop().

Plus besoin de mettre un ‘Print’ à chaque ligne de votre programme, le décorateur va afficher chaque appel de fonction ainsi que la sortie de chaque ligne (équivalent de print) soit directement en sortie stderr (par défaut), ou dans un fichier avec les attributs @pysnooper.snoop(‘/mon/fichier/logs.log’).

### Installation

```bash
pip install pysnooper
```

### Exemple

Le code suivant:

```python
import pysnooper

@pysnooper.snoop()
def exposant2(valeur):
    return valeur**2

valeurs = [1, 3, 4, 6, 7, 8]

for valeur in valeurs:
    exposant2(valeur)
```

Affichera la sortie suivante:

```python
Starting var:.. valeur = 1
22:18:51.034947 call         4 def exposant2(valeur):
22:18:51.035769 line         5     return valeur**2
22:18:51.035877 return       5     return valeur**2
Return value:.. 1
Starting var:.. valeur = 3
22:18:51.036179 call         4 def exposant2(valeur):
22:18:51.036380 line         5     return valeur**2
22:18:51.036478 return       5     return valeur**2
Return value:.. 9
Starting var:.. valeur = 4
22:18:51.036743 call         4 def exposant2(valeur):
22:18:51.036949 line         5     return valeur**2
22:18:51.037041 return       5     return valeur**2
Return value:.. 16
Starting var:.. valeur = 6
22:18:51.037343 call         4 def exposant2(valeur):
22:18:51.037562 line         5     return valeur**2
22:18:51.037658 return       5     return valeur**2
Return value:.. 36
Starting var:.. valeur = 7
22:18:51.037981 call         4 def exposant2(valeur):
22:18:51.038246 line         5     return valeur**2
22:18:51.038535 return       5     return valeur**2
Return value:.. 49
Starting var:.. valeur = 8
22:18:51.038855 call         4 def exposant2(valeur):
22:18:51.039045 line         5     return valeur**2
22:18:51.039170 return       5     return valeur**2
Return value:.. 64
```

Un bon outil pour faciliter le debug Python et pour analyser, pas à pas, les différents appels de fonction. C’est donc aussi un outils d’apprentissage du fonctionnement de Python qui fera de vous un meilleur développeur.

Pour plus d’information, consultez la page GitHub de l’outils [ici](https://github.com/cool-RR/PySnooper).