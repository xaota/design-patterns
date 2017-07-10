Базовой стратегией выбора элементов будет выбор всех элементов коллекции
```javascript
class BaseSelector {
  select(list) {
    return list;
  }
}
```

Реализуем несколько стратегий переопределяющих правила выбора элементов:

Стратегия выбора чётных чисел
```javascript
class EvenNumbersSelector extends BaseSelector {
  select(list) {
    return list.filter(n => n % 2 === 0);
  }
}
```

Стратегия выбора нечётных чисел
```javascript
class OddNumbersSelector extends BaseSelector {
  select(list) {
    return list.filter(n => n % 2 === 1);
  }
}
```

Стратегия выбора чисел из отрезка
```javascript
class NumbersFromRangeSelector extends BaseSelector {
  constructor(begin, end) {
    this.begin = begin;
    this.end   = end;
  }

  select(list) {
    let min = this.begin;
    let max = this.end;
    return list.filter(n => min <= n && n <= max);
  }
}

```

Используем реализованные стратегии
```javascript
let list = [1, 2, 3, 4, 5, 6, -4, -1, -455, 2, 1, 456, 783, 12, 45, 90, 24] ;

let even   = new EvenNumbersSelector;
let odd    = new OddNumbersSelector;
let ranged = new NumbersFromRangeSelector(1, 20);

even  .select(list); // 2, 4, 6, -4, ...
odd   .select(list); // 1, 3, 5, -1, ...
ranged.select(list); // 1, 2, 3, ..., 12
```