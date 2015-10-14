# transformar

Como sabemos las funciones son cajas negras que reciben un input, aplican una transformación y retornan un output.

Para ejemplificar crearemos un objeto círculo y función que transforma círculos en cuadrados.

Creamos el input:

```js
// type Circle = { radius: Number, color: String }
let circle = {
  radius: 5,
  color: "red"
};
```

Una función que lo transforma.

```js
// toSquare : Circle -> Square
let toSquare = (circle) => {
  return {
    side: circle.radius * 2,
    color: circle.color
  };
};
```

Y una aplicamos la transformación para obtener el output.

```js
let square = toSquare(circle);
```


La funcion `toSquare` recibe un círculo y lo transforma en un cuadrado. 



Pero que pasa cuando tenemos un lista de circulos y lo queremos transformar en una lista de cuadrados?

```js
let circles = [
  {
    radius: 5,
    color: "red"
  },
  {
    radius: 5,
    color: "green"
  },
  {
    radius: 5,
    color: "blue"
  }
];
```

Nuestra función no funciona con arrays...

```js
let squares = toSquare(circles); // { color: undefined, side: NaN }

```

Pero podemos implementar una que funcione, la llamaremos toSquares y recibirá un array de circulos y retornará un array de cuadrados

```js
// toSquares : [Circle] -> [Square]
let toSquares = (circles) => {
  // creamos un array vacio que contendrá todos los cuadrados
  let squares = [];
  
  // iteramos el array de circulos
  for (let i = 0; i < circles.length; i++) {
    // obtenemos el circulo de la iteracción actual
    let circle = circles[i];

    // transformamos el circulo en un cuadrado
    let square = toSquare(circle);

    // y lo añadimos al array inicial
    squares.push(square);
  }
  
  // por fin retornamos todos los cuadrados
  return squares;
};
```

```js
// toSquare : Circle -> Triangle
let toTriangle = (circle) => {
  let side = 3 * circle.radius / Math.sqrt(3);
  return {
    base: side
    height: Math.sqrt((side * side) + ((side / 2) * (side / 2)))
  };
};
```

```js
let toTriangles = (circles) => {
  let triangles = [];
  
  for (let i = 0; i < circles.length; i++) {
    let circle = circles[i];
    let triangle = toTriangle(circle);
    triangles.push(square);
  }

  return triangles;
}
```

Hmmm... esta función es sospechosamente parecida a la que transformaba círculos en cuadrados.

```js
let toAnotherShape = (circle, shapeType) => {
  if (shapeType === "circle") {
    return toCircle(circle);
  } else if (shapeType === "square") {
    return toSquare(circle);
  } else {
    return circle;
  }
};

let toAnotherShapes = (circles, shapeType) => {
  let otherShapes = [];
  
  for (let i = 0; i < circles.length; i++) {
    let circle = circles[i];
    let triangle = toAnotherShape(circle, shapeType);
    otherShapes.push(square);
  }

  return otherShapes;
}

let squares = toAnotherShapes(circles, "square");

```

Vamos mejorando, hemos encapsulado la iteración del array en una sola función pero si queremos utilizar la función `greenify` que pinte los círculos de verde tenemos que repetir el bucle y aplicar la función para cada círculo. Tiene que haber una abstracción mejor.

Creamos una función que transforme cualquier círculo en un círculo verde.

```js
let greenify = (circle) => {
  return Object.assign({}, circle, {
    color: "green"
  });
};
```

```js
let transformEach = (transform, things) => {
  let otherThings = [];
  
  for (let i = 0; i < things.length; i++) {
    let thing = things[i];
    let anotherThing = transform(thing);
    otherThings.push(anotherThing);
  }

  return otherThings;
}

let squares = transformEach(toSquare, circles);
let greenCircles = transformEach(greenify, circles);
```

La función que acabamos de crear ya existe pero con otro nombre.
Se llama `map` y está implmentada como un metodo en el objecto Array de javascript.

```js
let squares = circles.map(toSquare);
```


