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
3. Ecrire une fonction `handleChange` qui permettra de gérer les saisies dans n'importe quel champ, et qui sera donc associé au `onChange` de chaque champ.

**Répéter la même chose** avec un composant `Signup`, qui aura les mêmes champs, avec en plus un champ additionnel `confirmPassword`. Le but n'est pas de tout refaire from scratch... Vous allez donc forcément avoir à dupliquer du code !

## 2. `useEffect`

`useEffect` permet de déclencher des actions (par ex. des appels API) de façon "automatique". On le compare souvent au cycle de vie des composants-classes, mais le fonctionnement est assez différent !

### Exemple

Utilisation typique (je n'écris pas tout sinon ça vous enlève tout l'exercice !) :

```javascript
function UsersList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // lancer un appel API  puis appeler `setUsers` pour
    // stocker les utilisateurs récupérés
  }, []); // --> surtout ne pas oublier le tableau vide !
}
```

### Exercice

1. Compléter l'exemple :smirk: pour appeler l'API GitHub : <https://api.github.com/users>
2. Faire un `map` pour afficher au moins le login de chaque user, ainsi qu'un bouton (pour l'instant non fonctionnel) qui permettra de le sélectionner
3. Ajouter un deuxième state qui servira à stocker l'id d'un utilisateur, quand on clique sur le bouton ajouté précédemment.
4. Ajouter un troisième state qui servira à stocker un objet contenant les _détails_ d'un utilisateur (l'initialiser à `null`).
5. Ajouter un deuxième `useEffect`, avec dans son "tableau de dépendances" le "state" correspondant à l'id de l'utilisateur sélectionné. Dans ce `useEffect`, appeler le endpoint permettant d'obtenir les détails d'un utilisateur : <https://api.github.com/users/ID> (remplacer `ID` par l'id numérique stocké dans le "2ème state"). Stocker les data obtenues dans le state mis en place dans le point précédent. Vérifier que le 3ème state se met à jour avec de nouvelles données quand on clique sur un autre utilisateur.
6. Afficher quelques-unes des data récupérées via le endpoint de "détails d'un utilisateur" : par exemple la `location` et les `followers`.

## 3. Custom hook pour gérer un formulaire

Si tout s'est bien passé, dans les formulaires de la section 1 sur `useState`, vous devez avoir un `handleChange` qui ressemble à ça (j'ai destructuré les data imbriquées dans l'event) :

```javascript
const handleChange = ({ target: { name, value } }) =>
  setFormData((prevFormData) => ({
    ...prevFormData,
    [name]: value,
  }));
```

Et ce, dans chacun des formulaires `Signin` et `Signup`.

Ce n'est pas "horrible" en termes de quantité de code dupliqué, mais on peut faire mieux !

### Exemple de custom hook

Celui-ci n'a pas un grand intérêt, c'est juste pour l'exemple (gérer un compteur) :

```javascript
// src/hooks/useCounter.js
import { useState } from 'react';

export default function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const decrement = () => setCount((prevCount) => prevCount - 1);
  const increment = () => setCount((prevCount) => prevCount + 1);

  return [count, decrement, increment];
}
```

Dans le code qui l'utilise (on a importé `useCounter`) :

```javascript
function DummyCounter() {
  const [count, dec, inc] = useCounter();

  return (
    <div>
      <button type="button" onClick={dec}>
        -
      </button>
      {count}
      <button type="button" onClick={inc}>
        +
      </button>
    </div>
  );
}
```

### Exercice : factoriser le `handleChange` dans un custom hook

L'idée est d'écrire un custom hook `useForm` qui va renvoyer, en plus de `formData` et `setFormData`, la méthode `handleChange`.

On pourra alors éliminer la duplication de code entre `Signin` et `Signup` en utilisant `useForm` au lieu de `useState`.

## 4. Gérer les ajouts dans un tableau avec un custom hook.

On va créer une petite app de "todo-list".

1. Créer un composant `TodoList` contenant un state `tasks` (et `setTasks`) initialisé via `useState` avec un tableau vide.
2. Créer un composant `NewTask` qui va être appelé depuis `TodoList`. Ce sera un simple formulaire avec un input permettant de saisir une nouvelle tâche. Vous pouvez (pas obligatoire) réutiliser le custom hook `useForm` écrit précédemment.
3. Passez une fonction `onSubmit` de `TodoList` à `NewTask` via les props. Pour l'instant, la fonction écrite dans le parent peut être une fonction vide : `() => {}`.
4. Sur la balise `form`, associez au `onSubmit`, non pas directement le `onSubmit` passé par le parent, mais une fonction qui va :

   1. empêcher l'event `submit` de vous faire sortir de la page,
   2. appeler le `onSubmit` passé par le parent, en lui passant la valeur de l'input.

5. Compléter la fonction vide écrite en 3. Elle va prendre en entrée l'intitulé de la nouvelle tâche. Puis à partir de cet intitulé, créer un objet ayant la forme `{ id: <un id numérique séquentiel>, label: 'intitulé de la tâche' }` (voir ci-dessous pour une façon simple de créer un id séquentiel). Ensuite, elle va stocker cet objet dans le tableau en appelant `setTasks`.
6. Le vif du sujet : créer un custom hook pour gérer l'addition d'un élément à un tableau (logique qui se retrouve souvent dupliquée dans une app React). Il renverra, en plus de la valeur et de la fonction permettant de la muter, une fonction `addItem` qui, quand on l'appellera, ajoutera un élément au tableau.

**Annexe :** fonction permettant de générer des `id` séquentiels (c'est un exemple de _closure_) :

```javascript
const getId = (() => {
  let value = 0;
  return () => {
    value += 1;
    return value;
  };
})();
```
