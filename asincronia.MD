# como manejar las asincronia en javascript
## por christian segura

la asincronia es un tema de suma importancia en Javascript, ya que estaremos tratando siempre con ella
ya sea manejando un evento en el DOM, realizando peticiones AJAX, o menejando eventos en el servidor con Node.js

existen 3 formas de manejar este comportamiento

- CallBacks.
- Promises
- Async/Await


## CallBacks

un Callback es un funcion que recibe como argumento otra funcion y la ejecuta una vez haya terminado sun funcionamiento.

existe un standar/patron para le creacion de callbacks, la cual dice
que el primer arguemento que se retorna sera un error, en caso tal de
que hubiese uno, de lo contrarion se retorna null/false

acontinuacion veremos un pequenio ejemplo

```js
const addItemToArrayAsync = (array, item, cb) => {
  array.push(item),
  cb(null, array)
}

let array = [1, 2]
addItemToArrayAsync(array, 3, (err, data) => {
  if (err) {
    throw err
  }
  console.log(data)
  // [1, 2 ,3]
})
```

como podemos observar la funcion addItemTOArraySync recibe como argumentos un array, un elemento y una funcion, la funcion tiene como primer argumento un error, y como segundo nos devuelve un array con un nuevo elemento.

vamos realizar otro ejemplo con las misma funcion pero esta vez para simular la asincronia, utilizaremos, setTimeOut una funcion del core JavaScript

```js
const addItemToArrayAsync = (array, item, cb) => {
  setTimeout(function () {
    array.push(item),
      cb(null, array)
  }, 5000)
}

let array = [1, 2]
addItemToArrayAsync(array, 3, (err, data) => {
  if (err) {
    throw err
  }
  console.log(data)
  // after of 5 seconds [1, 2 ,3]
})
```
ahora vamos el corpontamiento si tuviesemos un callback.
```js
const addItemToArrayAsync = (array, item) => {
  setTimeout(function () {
    array.push(item),
  }, 5000)
}

let array = [1, 2]
addItemToArrayAsync(array, 3)
console.log(array) // 1, 2

```
como podemos observar, el comportamiento no es esperado por nosotros


## Promesas

las promesas son incluyeron de forma nativa en el standar 2015(es6).
con esto en teoria se viene a solucionar un problema conocido como el callback hell

el callback hell es como se se daba necesitamos esperar la respuesta asincrona de alguna funcion, y esta a su vez ejecutaba otra funcion asincrona, ...etc

![callback hell image](https://adrianalonso.es/wp-content/uploads/2019/01/streetcallback.png "callback hell image1")

ahora vamos a crear la misma funcionlidad de agregar un elemento a un array de forma asincrona

```js
const addItemToArrayAsync = (array, element) => {
  return new Promise((reject, resolve) => {
    if (!Array.isArray(array)) {
      return reject(new Error ('no is a valid array'))
    }
    seTimeout(() => {
      return resolve(array.push(element))
    })
  })
}
```

como podemos observar crear una promesa es muy diferente a crear un callback.

para crear una promesa disponemos del constructor  ```new Promise```
que recibe una funcion con dos argumentos, ```reject``` y ``` resolve ```

``` reject ``` se utiliza para retornar un error. <br />
``` resolve ``` se utiliza para retorar la informacion.

ahora vamos a ver como se trabajar o resolver una promesa

```js
let array = [1, 2]
addItemToArrayAsync(array, 3)
.then(data => console.log(data)) // expected output [1, 2, 3]
.catch(function (err) {
  console.error('ocurrio un error' , err)
})

```

para obtener los datos devueltos por una promesa simplemente debemos al metodo ``` then ```, que recibe como parametro una funcion, la cual nos retorna el valor despues de realizar la operacion asincrona. para menejar los errores las promesas nos provee de un  metodo llamado ```  catch ``` el cual espera como argumento o parametro una funcion la cual nos retornara el error.


ahora veremos algo un poco mas interesante

```js
let array = [1, 2]
addItemToArrayAsync(array, 3)
.then(function (data) { return addItemToArrayAsync(data, 4) })
.then(function (data) { return addItemToArrayAsync(data, 5) })
.then(function (data) { return addItemToArrayAsync(data, 6) })
.then(function (data) { return addItemToArrayAsync(data, 7) })
.then(function (data) { return addItemToArrayAsync(data, 8) })
.then(function (data) { return addItemToArrayAsync(data, 9) })
.catch(function (err) {
  console.error(err.message)
})
```

en este ejemplo podemos observar como se puede anidar promesas sin que se genere el denominado callback hell, o un promise hell

acontinuacion veremos otras formas de realizar este mismo ejemplo. <br />
<strong>arrow function:</strong>
```js
let array = [1, 2]
addItemToArrayAsync(array, 3)
.then(data => addItemToArrayAsync(data, 4))
.then(data => addItemToArrayAsync(data, 5))
.then(data => addItemToArrayAsync(data, 6))
.then(data => addItemToArrayAsync(data, 7))
.then(data => addItemToArrayAsync(data, 8))
.then(data => addItemToArrayAsync(data, 9))
.catch(err => console.error(err.message))
```
<strong>Promise all:</strong>
```js
let array = [1, 2]
let promises = []
for (let i = 3; i < 10; i++) {
  promises.push(addItemToArrayAsync(array, i))
}
Promise.all(promises)
.then(values => {
  console.log(values[0]) // [1, 2, 3]
  console.log(values[1]) // [1, 2, 3]
  ...                   //  [1,2 ,3 ...9]
})
.catch(err => console.error(err))

```

## Async/Await

esta es la ultima forma de manejar las funciones asincronas.
veamos su sintaxis.
```js
let array = [1, 2]
let promises = []
for (let i = 3; i < 10; i++) {
  promises.push(addItemToArrayAsync(array, i))
}


async function addItems () {
  try {
    let data = await Promise.all(promises)
    console.log(data) // [[1, 2, 3], [1, 2, 3, 4]]
  } catch (e) {
    console.error(e)
  }
}
```
como podemos observar asyn/await depende de que una funcion tenga la palabra reservada ``` async ```, y para resolver la promesa utilizamos la palabra resevarda ``` await ```.

tambien podemos resolver multiples promesas al tiempo haciendo uso de ``` Promise.all ```

```js
let array = [1, 2]

const addItems = async (array, item) => {
  try {

  } catch (e) {

  }
}
```