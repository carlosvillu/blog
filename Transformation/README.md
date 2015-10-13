# transformar

```js
// type Circle = { radius: Number, color: String }
let circle = {
  radius: 5,
  color: "red"
};

// toSquare : Circle -> Square
let toSquare = (circle) => {
  return {
    side: circle.radius * 2,
    color: circle.color
  };
};

let toTriangle = (circle) => {
  let side = 3 * circle.radius / Math.sqrt(3);
  return {
    base: side
    height: Math.sqrt((side * side) + ((side / 2) * (side / 2)))
  };
};
```

Tenemos un círculo y lo queremos *transformar* en un cuadrado.
La funcion `toSquare` recibe un círculo y lo transforma en un cuadrado. 

```js
var square = toSquare(circle);
```

Pero que pasa cuando tenemos un lista de circulos y lo queremos transformar en una lista de cuadrados?

```js
var circles = [
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
var squares = toSquare(circles); // { color: undefined, side: NaN }

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

Vamos mejorando, hemos encapsulado la iteración del array en una sola función pero si queremos utilizar la función `greenify` tenemos que repetir el bucle y aplicar la función para cada círculo. Tiene que haber una abstracción mejor.

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
    let anotherThing = tranform(thing);
    otherThings.push(anotherThing);
  }

  return otherThings;
}

var squares = transformEach(toSquare, circles);
var greenCircles = transformEach(greenify, circles);
``




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

let squares = circles.map(toSquare);
```
