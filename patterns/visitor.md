Создадим классы, описывающие животных.
Статический метод `is(animal)` каждого класса используем для проверки вида животного

Родительский класс для всех животных
```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  accept(security) {
    return security.validate(this);
  }

  static is(object) {
    return object instanceof Animal;
  }
}
```

Класс, описывающий кошек
```javascript
class Cat extends Animal {
  static is(object) {
    return object instanceof Cat;
  }
}
```

Класс, описывающий собак
```javascript
class Dog extends Animal {
  static is(object) {
    return object instanceof Dog;
  }
}
```

Класс, описывающий действия охраны
Базовый "охранник" будет "пропускать" всех животных
```javascript
class AnimalSecurity {
  validate(animal) {
    return Animal.is(animal);
  }
}
```

Класс, описывающий "охранника" в кошачьем клубе
Пропускает только кошек
```javascript
class CatSecurity extends AnimalSecurity {
  validate(animal) {
    return Cat.is(animal);
  }
}
```

Класс, описывающий "охранника" в собачьем клубе
Пропускает только собак
```javascript
class DogSecurity extends AnimalSecurity {
  validate(animal) {
    return Dog.is(animal);
  }
}
```

Применим реализованный функционал
```javascript
let cat = new Cat('Персик');
let dog = new Dog('Спотти');

let catSecurity = new CatSecurity;
let dogSecurity = new DogSecurity;

cat.accept(catSecurity); // true
cat.accept(dogSecurity); // false
dog.accept(catSecurity); // false
dog.accept(dogSecurity); // true
```
