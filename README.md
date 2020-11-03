# Dojo Hooks

:point_right: Commencez par cloner ce repo et installer les dépendances !

Objectif :

- savoir utiliser `useState`,
- savoir utiliser `useEffect`,
- écrire un "custom hook" pour gérer un formulaire,
- écrire un "custom hook" pour gérer l'ajout dans un tableau

## 1. `useState`

Plutôt que d'avoir **un** "gros" state dans un composant-classe, `useState` permet de créer plein de petits states.

### Exemple

Pour gérer un compteur de clics :

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  // utilisation de la forme "callback" pour
  // baser la nouvelle valeur sur l'ancienne
  const handleClick = () => setCount((prevCount) => prevCount + 1);

  return (
    <button type="button" onClick={handleClick}>
      {count}
    </button>
  );
}
```

### Exercice

1. Ecrire un formulaire de login (composant `Signin`) avec au deux champs input (email et password), chacun ayant un attribut `name` (`email` et `password`, donc !).
2. Dans ce composant, initialiser un state avec `useState`, pour stocker **toutes** les données du formulaire. Sa valeur initiale : `{ email: '', password: '' }`.
3. Ecrire une fonction `handleChange` qui permettra de gérer les saisies dans n'importe quel champ, et qui sera donc associé au `onClick` de chaque champ.

**Répéter la même chose** avec un composant `Signup`, qui aura les mêmes champs, avec en plus un champ additionnel `confirmPassword`. Le but n'est pas de tout refaire from scratch... Vous allez donc forcément avoir à dupliquer du code !

## 2. `useEffect`
