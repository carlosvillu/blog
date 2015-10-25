# Separar el grano de la paja

Me gustan los M&M's pero de vez en cuando me encuentro uno con un cacahuete malo.
Además tengo preferencia por los de color azul y amarillo.
Me han ofrecido una Máquina Generadora de M&M's®. Desafortunadamente no puedo tunearla para que solo genere M&M's azules y amarillos, tampoco puedo hacer que todos los M&M's tengan cacahuetes siempre buenos.

Sólo existe una de estas máquinas en el mundo entero y se utiliza del siguiente modo:

```js
// 20 es el número de M&M's que queremos generar
let randomMNMs = MagicMachine.run(20);
```

Haciendo ingenyeria inversa he montado este modelo de su implementación.
Es un *singleton* con un método `run` que recibe la cantidad de M&M's que queremos generar.

```js
let MagicMachine = (() => {
  //  generateRandomMNM : () -> {color: String, hasBadPeanut: Bool}
  let generateRandomMNM = () => {
    // selecciona un color de entre los colores posibles de M&M's
    let generateRandomMNMColor = () => {
      let colors = ["brown", "orange", "red", "yellow", "green", "blue"];
      let randomIndex = Math.round(Math.random() * colors.length);

      return colors[randomIndex];
    };
    
    // 1 de cada 10 cacuhetes són malos
    let isRandomPeanutBad = () => Math.round(Math.random() * 10) === 1;

    return {
      color: generateRandomMNmColor(),
      hasBadPeanut: isRandomPeanutBad()
    };
  };

  //  generateRandomMNMs : Number -> [{color: String, hasBadPeanut: Bool}]
  let generateRandomMNMs = (quantity) => {
    let bag = [];

    for (let i = 0; i < quantity; i++) {
      bag.push(generateRandomMNM());
    } 

    return bag;
  };

  return {
    run: generateRandomMNMs
  };
}());
```

Como he dicho anteriormente, no hay como tunear la máquina.
Ahora mismo lo hago todo a mano, los cojo uno a uno, si són azules o amarillos los pongo en un bowl, lo que sobra lo tiro. Perdón? Si, lo tiro. Es una máquina generadora de M&M's, no los voy a guardar.
La única forma que tengo de saber si los cacahuetes están buenos es probandolos. Es como es método de encender una cerilla para saber si funciona.
Tiene que haver una manera mejor. Lo que realmente quiero es una máquina que recibe todos los M&M's generados y los transforma en los M&M's que me gustan.

[M&M's generados] -> [M&M's que me gustan]

Ahora mismo en nuestra caja de herramientas, [https://carlosvillu.com/funciones-de-orden-superior-maps/](tenemos la función map), probemos a ver si nos sirve para resolver el problema:

Una solución posible seria pintar todos los M&M's de azul o amarillo, y transformar los cacahuetes malos en cacahuetes buenos.

```js
let paintBlueAndFixBadPeanut = (mNm) => {
  return {
    ...mNm,
    color: "blue",
    hasBadPeanut: false
  };
};

let mNms = Machine.run(40);
let mNmsThatILike = map(paintBlueAndFixBadPeanut, mNms);
```

Desafortunadamente en la vida real la cosas no funcionan así, (si pero y la máquina que genera M&M's infinitos? shh, no importa), un cacahuete malo no puede volver a ser bueno, y si pinto los cacahuetes de azul lo más probable es que tenga una intoxicacion alimentaria. La única cosa que es mágica es la máquina que los genera.
Tendremos que jugar con lo que ya tenemos y filtrar los elementos que nos interesan. Para filtrar necessitamos un critério de selección. Nuestro criterio es el siguiente; si no tiene cacahuete malo y es amarillo o azul, pasa el filtro.

Lo que necesitamos es una M&M Filtering Machine®!

```js
let shouldPassTheFilter = (mNm) => {
  return (
    !mNm.hasBadPeanut && 
    (mNm.color === "blue" ||
     mNm.color === "yellow"));
};
```

Estas funciones que retornam **true** o **false** se llaman predicados.
Y nos ayudarán a seleccionar una sub colección de otra colección.

Un pequeño aparte sobre predicados.
Predicados son funciones que retornan un valor boolean. Dentro de la función que hemos creado hay varias expressiones que evaluan a un boolean. Ponemos decomponer la función en tres funciones más sencillas.

```js
let isYellow = (mNm) => mNm.color === "yellow";
let isBlue = (mNm) => mNm.color === "blue";
let isGood = (mNm) => !mNm.hasBadPeanut;
```

Como volvemos a combinarlas?
Sustituyendo las expresiones por llamadas a las funciones.

```js
let shouldPassTheFilter = (mNm) => {
  return isGood(mNm) && (isBlue(mNm) || isYellow(mNm));
};
```

La repetición de mNm en cada predicado es redundante.
Pero podemos ir un poco más allá creando funciones que combinan predicados.

```js
// either recibe dos predicados y 
// retorna una nueva función que los combina con un "or"
let either = (predicateOne, predicateTwo) => (value) => {
  return predicateOne(value) || predicateTwo(value);
};

// both recibe dos predicados
// y los combina con un "and"
let both = (predicateOne, predicateTwo) => (value) => {
  return predicateOne(value) && predicateTwo(value);
};

let shouldPassTheFilter = both(isGood, either(isYellow, isBlue));
```

Mucho mejor. Hemos empezado con predicados simples, hemos creado funciones `either` y `both` que combinan predicados. Y hemos acabado con una nueva función, que es también un predicado. Muy bién, y ahora qué?
Ahora nos falta aplicar esta función a cada M&M para saber si debería pasar el filtro o no.
Os acordais de la [https://carlosvillu.com/funciones-de-orden-superior-maps/](función map del artículo anterior)?
Que pasaría si utilizaramos map con nuestro predicado?

```js
let map = (transform, things) => {
  let otherThings = [];
  
  for (let i = 0; i < things.length; i++) {
    let thing = things[i];
    let anotherThing = transform(thing);
    otherThings.push(anotherThing);
  }

  return otherThings;
}

var mNms = map(shouldPassTheFilter, [
  {color: "yellow", hasBadPeanut: false},
  {color: "blue", hasBadPeanut: true}]); // [true, false]
```

Map aplica la función a cada elemento de la colección.
Lo que queremos es el valor del elemento para el cual el predicado haya retornado true. No queremos los valores [true, false, false, ...]:
En lugar de transformar, queremos filtrar:

```js
let filter = (predicate, things) => {
  let otherThings = [];
  
  for (let i = 0; i < things.length; i++) {
    let thing = things[i];
    
    // Aquí, a diferencia de map,
    // filter en lugar de transformar cada cosa y ponerla en un contenedor,
    // primero verificamos que pasa unos determinados criterios.
    // Si los pasa, la añadimos al contenedor
    if (predicate(thing)) {
      otherThings.push(anotherThing);
    }
  }

  return otherThings;
}
```

Estamos listos para crear nuestra máquina de filtrar.

```js
let FilterMachine = {
  run: (mNms) => filter(both(isGood, either(isYellow, isBlue)), mNms)
};
```

Ya está? Ha sido fácil.
Componiendo pequeñas funciones nuestro código ha quedado bastante conciso.

Para hacer mi vida más fácil me compraré una caja y un tubo.
Pondré las dos máquina dentro de la caja y las uniré con un tubo por donde pasarán los M&M's.

```js
// aquí está la caja
let MyMagicMachine = (function (FilterMachine, MagicMachine) {
  return {
    // ---------------------------------aquí está el tubo
    run: (quantity) => FilterMachine.run(MagicMachine.run(quantity))
  };
}(FilterMachine, MagicMachine));
```

```js
let mNmsThatILike = MyMagicMachine.run(20);
```

De los 40 M&M's generados a lo mejor solo me cómo 6. Es lo que pasa cuando filtras cosas, siempre acabas con un cantidad igual o menor de la que haz empezado. No hay problema, siempre puedo volver generar más.
