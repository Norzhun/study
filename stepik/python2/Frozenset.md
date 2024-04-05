Замороженное множество (`frozenset`) также является встроенной коллекцией в Python. Обладая характеристиками обычного множества, замороженное множество не может быть изменено после создания.
Для создания замороженного множества используется встроенная функция `frozenset()`, которая принимает в качестве аргумента другую коллекцию.

Приведенный ниже код:

```python
myset1 = frozenset({1, 2, 3})                         # на основе множества
myset2 = frozenset([1, 1, 2, 3, 4, 4, 4, 5, 6, 6])    # на основе списка
myset3 = frozenset('aabcccddee')                      # на основе строки

print(myset1)
print(myset2)
print(myset3)
```

выводит:

```no-highlight
frozenset({1, 2, 3})
frozenset({1, 2, 3, 4, 5, 6})
frozenset({'e', 'd', 'c', 'b', 'a'})
```
### Операции над замороженными множествами

Над замороженными множествами можно производить все операции, которые можно производить над обычными множествами:

- объединение множеств: метод `union()` или оператор `|`;
- пересечение множеств: метод `intersection()` или оператор `&`;
- разность множеств: метод `difference()` или оператор `-`;
- симметрическая разность множеств: метод `symmetric_difference()` или оператор `^`.

Приведенный ниже код:

```python
myset1 = frozenset('hello')
myset2 = frozenset('world')

print(myset1 | myset2)
print(myset1 & myset2)
print(myset1 ^ myset2)
```

выводит:

```no-highlight
frozenset({'l', 'w', 'e', 'h', 'r', 'd', 'o'})
frozenset({'l', 'o'})
frozenset({'d', 'h', 'w', 'e', 'r'})
```
**Примечание 1.** Будучи изменяемыми, обычные множества не могут быть элементами других множеств. Замороженные множества являются неизменяемыми, а значит могут быть элементами других множеств.

Приведенный ниже код:

```python
sentence = 'The cat in the hat had two sidekicks, thing one and thing two.'

words = sentence.lower().replace('.', '').replace(',', '').split()

vowels = ['a', 'e', 'i', 'o', 'u']

consonants = {frozenset({letter for letter in word if letter not in vowels}) for word in words}

print(*consonants, sep='\n')
```

выводит (порядок элементов может отличаться):

```no-highlight
frozenset({'d', 'h'})
frozenset({'h', 't'})
frozenset({'n', 'h', 'g', 't'})
frozenset({'n'})
frozenset({'c', 't'})
frozenset({'n', 'd'})
frozenset({'w', 't'})
frozenset({'s', 'c', 'k', 'd'})
```

**Примечание 2.** Методы, изменяющие множество, отсутствуют у замороженных множеств:

- `add()`
- `remove()`
- `discard()`
- `pop()`
- `clear()`
- `update()`
- `intersection_update()`
- `difference_update()`
- `symmetric_difference_update()`
**Примечание 3.** Мы можем сравнивать простые (тип `set`) и замороженные множества (тип `frozenset`).

Приведенный ниже код:

```python
myset1 = set('qwerty')
myset2 = frozenset('qwerty')

print(myset1 == myset2)
```

выведет:

```no-highlight
True
```