В `JavaScript` итераторы дают возможность сделать «перебираемыми» любые объекты с помощью цикла `for .. of`, а для создания итераторов есть специальный ключ `Symbol.iterator` (статический? элемент примитивного типа `Symbol`).

Итератор - метод, который должен возвращать объект, содержащий метод `next()`, который, в свою очередь, возвращает объект, содержащий поля `{boolean} done` и `{any} value?` (следующий элемент последовательности):

```javascript
let range = {
  begin : 0,
  end   : 10,

  [Symbol.iterator]() {
    let current = this.begin;
    let last    = this.end;
    return {
      next() {
        return current < last
          ? { done: false, value: current++ }
          : { done: true };
      }
    }
  }
}
```

У большинства элементов есть встроенные итераторы, которые можно получить явным образом, например:
```javascript
let iterator = array[Symbol.iterator]();
```

Если функционал по перебору `next()` предоставляется самим объектом, то можно вернуть `this` в качестве итератора.

---

Разумеется, итераторы могут быть бесконечными, могут быть непривязаны к объекту, и могут иметь хитрую внутреннюю логику.

Создадим итератор по элементам массива:
```javascript
function iterator(array) {
  let index = 0;
  return {
    next() {
      return index < array.length
        ? { done: false, value: array[index++] }
        : { done: true };
       }
    }
}
```

Инициализируем итератор и можем использовать метод `next()` для поочередного доступа к парам ключ-значение в объекте:
```javascript
let it = iterator([1, 2, 3, 4, 5, 6, 7]);

let first   = it.next(); // { done: false, value: 1 }
let second  = it.next(); // { done: false, value: 2 }
// ...
let seventh = it.next(); // { done: false, value: 7 }
let eighth  = it.next(); // { done: true }
```

При написании итераторов следует уделять серьезное внимание поддержке внутреннего состояния.

`JavaScrpit` предоставляет возможность возможность использования корутин в качестве фабрики итераторов (за счёт возможности корутин-функций поддерживать собственное состояние). Это называется ленивым итератором или __генератором__.

Генераторы сами возвращают объект с методом `next()`, возвращающий объекты вида `{done, value}`.
```javascript
function* generator() {
  let index = 0;
  while (true)
    yield index++;
}

let gen = generator();
gen.next().value; // 0
gen.next().value; // 1
gen.next().value; // 2
// ... это можно делать бесконечно
```

Генераторы можно "перезапускать", передав в `next` не `undefined` параметр, но только, если генератор уже был запущен, а вы обработаете этот случай в коде (точка возврата `yield` получит переданное значение):
```javascript
function* fibonacci() {
  let first = 1,
     second = 1;

  while (true) {
    let current = second;
    second = first;
    first = first + current;
    let reset = yield current;
    if (reset) { // перезапуск
      first  = 1;
      second = 1;
    }
  }
}

let sequence = fibonacci();
sequence.next()    .value     // 1
sequence.next()    .value     // 1
sequence.next()    .value     // 2
sequence.next()    .value     // 3
sequence.next()    .value     // 5
sequence.next(true).value     // 1 - перезапуск
sequence.next()    .value     // 1
sequence.next()    .value     // 2
sequence.next()    .value     // 3
```

__Упрощёный синтаксис__

Также, с помощью итераторов и генераторов можно создавать новые массивы и другие итераторы, например, удвоим исходный массив:
```javascript
// it - итератор, см выше
let array    = [for (i in it) i * 2]; // создаём массив
let iterable = (for (i in it) i * 2); // создаём итератор
```
Разница в том, что вызовы `iterable.next()` позволяют осуществлять "ленивую" обработку последовательностей данных, когда данные обрабатываются по запросу, поскольку если входные данные представляют собой большой массив либо даже бесконечный поток, создание нового массива может стать проблематичным (а также это проход по данным при создании массива).
