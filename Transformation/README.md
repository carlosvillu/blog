# transformar

Como sabemos las funciones son cajas negras que reciben un input, aplican una transformación y retornan un output.

Para ejemplificar crearemos un objeto círculo y función que transforma círculos en cuadrados.

Creamos el input:

```js
// ● = { radius: Number, color: String }
let circle = {
  radius: 5,
  color: "red"
};
```

Una función que lo transforma.

```js
// toSquare : ● -> ■
let toSquare = (circle) => {
  return {
    side: circle.radius * 2,
    color: circle.color
  };
};
```

Y aplicamos la transformación para obtener el output.

```js
let square = toSquare(circle);
```

Hasta aquí ningun problema.
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

Ahora nuestros circulos están dentro de un contenedor. Y no nuestra función no funciona con arrays...

```js
let squares = toSquare(circles); // { color: undefined, side: NaN }
```

Necesitamos una función que pueda entrar en el contenedor, transformar cada valor y volver a poner todo en un contenedor.
La llamaremos *toSquares* y recibirá un array de círculos y retornará un array de cuadrados.

```js
// toSquares : [●] -> [■]
let toSquares = (circles) => {
  // creamos un array vacio para acumulurará los cuadrados
  let squares = [];
  
  // iteramos el array de circulos
  for (let i = 0; i < circles.length; i++) {
    // obtenemos el circulo de la iteración actual
    let circle = circles[i];

    // transformamos el círculo en un cuadrado
    // con la función que habíamos creado
    let square = toSquare(circle);

    // y lo añadimos al array acumulador
    squares.push(square);
  }
  
  // por fin retornamos todos los cuadrados
  return squares;
};
```

Ya lo tenemos.

```js
let squares = toSquares(circles);
```

Pero ahora nos gustaría también transformar un círculo en un triangulo.
Tenemos la función que lo hace para un solo triangulo.

```js
// toTriangle : ● -> ▲ 
let toTriangle = (circle) => {
  let side = 3 * circle.radius / Math.sqrt(3);
  return {
    base: side
    height: Math.sqrt((side * side) + ((side / 2) * (side / 2)))
  };
};
```

Pero no la funcion que lo haga para una lista de triangulos.
Bueno, no pasa nada, si lo hemos podido hacer para cuadrados no será dificil de hacerla para triangulos.

```js
// toTriangles : [●] -> [▲]
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
Parece que lo que cambia es el tipo de forma

```js
// toAnotherShape : ● -> String -> ▲ | ■ | ●
let toAnotherShape = (circle, shapeType) => {
  if (shape === "circle") {
    return toTriangle(circle);
  } else if (shapeType === "square") {
    return toSquare(circle);
  } else {
    return circle;
  }
};

// toAnotherShapes : [●] -> String -> [▲] | [■] | [●]
let toAnotherShapes = (circles, shapeType) => {
  let otherShapes = [];
  
  for (let i = 0; i < circles.length; i++) {
    let circle = circles[i];
    let otherShape = toAnotherShape(circle, shapeType);
    otherShapes.push(square);
  }

  return otherShapes;
}

let squares = toAnotherShapes(circles, "square");

```

Vamos mejorando, hemos encapsulado la iteración del array en una sola función.
Pero pasar un shapeType me parece un *hack*. Además *toAnotherShape* funciona para cambios de tipo, pero y si tenemos una función que cambie el color?
Tenemos que repetir el bucle y aplicar la función para cada círculo. Tiene que haber una abstracción mejor.

Dada una función que reciba un círculo y devuelva un nuevo círculo de color verde:

```js
// greenify : ● -> ●
let greenify = (circle) => {
  return Object.assign({}, circle, {
    color: "green"
  });
};
```

La funcion toAnotherShapes ya no nos vale, no sabemos que transformación le vamos a aplicar pero nos gustaría mantener la lógica de iteración.
Si a partida no sabemos que transformación vamos a aplicar a cada elemento, hay que pasar la función adentro. Llamaremos a esa función **transform** y *toAnotherShapes* le llamaremos **transformEach**.

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
Se llama **map** y está implementada como un método del objecto Array de javascript desde la versión ES5.

```js
let squares = circles.map(toSquare);
let triangles = circles.map(toTriangle);
let greenCircles = circles.map(greenify);
```


