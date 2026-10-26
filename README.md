# Pattatras

Petit programme qui tire des nombres aléatoires entre 1 et 6457 et leur applique des règles d'affichage spécifiques. Le projet est une page statique publiée avec GitHub Pages.

## Fonctionnement

Cliquez sur le bouton "relancer" pour tirer un autre chiffre.

## Objectif

Parcourir une suite de nombres aléatoires de 1 à 6457 et, pour chaque nombre, afficher :

| Condition | Affichage |
|---|---|
| Multiple de 3 et de 5 | `Pattatras` |
| Multiple de 3 uniquement | `Patte` |
| Multiple de 5 uniquement | `Tatras` |
| Aucun des deux | Le nombre lui-même |

## Exemples

| Nombre | Résultat |
|---|---|
| 3480 | `Pattatras` |
| 5439 | `Patte` |
| 1090 | `Tatras` |
| 4019 | `4019` |

## Fonctionnement

1. Un nombre entier aléatoire est tiré entre 1 et 6457 inclus :

   ```javascript
   Math.floor(Math.random() * 6457) + 1;
   ```

2. La fonction `appliquerRegles(nombre)` détermine l'affichage. L'ordre des tests est important : le cas « multiple de 3 et de 5 » doit être vérifié en premier, sinon il serait masqué par les deux autres.

   ```javascript
    function appliquerRegles(nombre){
        let resulat;
        if (nombre % 3 === 0 && nombre % 5 === 0){
            resultat = "Pattatras";
        }else if(nombre % 3 === 0){
            resultat = "Patte";
        }
        else if(nombre % 5 === 0){
            resultat = "Tatras";
        }
        else{
            resultat = nombre;
        }
        return {'nombre' : nombre, 'resultat' : resultat};
    }

   ```

3. L'interface (Vue.js) affiche le résultat, le nombre tiré, et propose un bouton **Relancer** pour effectuer un nouveau tirage. Le style du résultat change selon la règle appliquée (`patte`, `tatras`, `pattatras`).

## Structure du projet

```
.
├── index.html   # Page, styles et interface
└── README.md
```

## Lancer le projet en local

Aucune installation n'est nécessaire : ouvrir `index.html` dans un navigateur suffit.

## Publication

Le site est publié via GitHub Pages à partir du dépôt `NatohG.github.io`.

Dépôt : <https://github.com/NatohG/NatohG.github.io>

## Technologies

- HTML / CSS
- JavaScript
- Vue.js
- GitHub Pages
