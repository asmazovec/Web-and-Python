## Функции Python ##

### Документация ###

Зарезервированное слово def предваряет определение функции. За ним должны следовать имя функции и заключенный в скобки список формальных параметров. Выражения, формирующие тело функции, начинаются со следующей строки и должны иметь отступ. Первым выражением в теле функции может быть строковый литерал - этот литерал является строкой документации функции, или док-строкой (`docstring`)  
```Python
def some_function(arg1, arg2, ..., argN):
    """Documentation"""
    expressions
```

*Пример документации на функцию*
```Python
def my_function():
    """
    Documentation

    with empty line.
    """
    pass
```

*Получить мета данные функции*

```Python
>>> my_function.__name__
'my_function'

>>> my_function.__doc__
'\n Documentation\n\n with empty line\n'

>>> help(my_function)
Help on function my_function in module __main__:

my_function()
    documentation

    with empty line
```

### Аргументы функции ###

*вызов функции*

```Python
def min(x, y):
    return x if x < y else y

min(1, 2)       # Аргументы посылаются кортежем
min(1, y=2)     # Можно явно соотносить значение с именем
min(x=1, y=2)
min(y=2, x=1)   # И даже в любом порядке
```

Можно подать в функцию неименованные аргументы.

```Python
def min(*args):
    res = float('inf')
    for x in args:
        res = x if x < res else res
    return res

min(92, 10, 62) # Несколько неименованных аргументов для поиска минимума.
min()           # Проблема - возвращаем значение даже при отсутствии элементов,
                # может быть не очень хорошо

xs = [1, 2, 3]  #
min(*xs)        # Разыменовывание списка, на вход функции поступает три аргумента.
```

**Моноид** - это операция удовлетворяющая следующим законам:

- ассоциативность `(a+b)+c = a+(b+c)`,
- является бинарной `a+b+c` - все они имеют имеют один тип, как и результат,
- имеет нейтральный элемент `(e)? e op x = x 1*x = x, x+0 = x`

**Функтор** - функция применяется к элементам, находящимся в некотором контексте, в функторе (список - функтор), объект к которому применима `fmap`.

```Python
X = [1, 2, 3]

>>> mp = map(lambda x:x*2, x)
>>> list(mp)
[2, 4, 6]
```

**Аппликативный функтор** - извлеченная упакованная функция (сама функция возвращает неупакованное значение), применяется к извлеченному упакованному значению и получает упакованные значения.

**Монада** - вы применяете функцию, возвращающую упакованное значение, к упакованному значению (`PyMonad`).  
Монада - тип, который позволяет строить цепочки вычислений. Между этими вычислениями передаются только монады, что и делает эти цепочки в некотором смысле универсальными.

*Примеры*

```Python
def positive_and_negative(x):
    return List(x, -x)

List(9) >> positive_and_negative    # Результатом станет монада List(9, -9)


def add_and_sub(x, y):
    return List(y + x, y - x)

List(2) >> add_and_sub(3)   # Вернет список List(5, -1)

List(2) >> positive_and_negative >> add_and_sub(3)  # Результатом станет List(5, -1, 1, -5)
```

```Python
def min(first, *rest):
    res = first
    for x in rest:
        res = x if x < res else
    retutrn res

>>> min("hello", ",", " ", "world")
' '

>>> min()   # Краш в случае отсутствия, но зато можно отследить ошибку.
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: min() missing 1 required positional argument: 'first'

>>> x = []
>>> min(*x)
```

Иногда нужен минимум по пустому значению. Указываем дефолтное значение в случае пустого списка аргументов. 
 
*Пример решения проблемы*

```Python
def min(*args, default=None):
    if not args:
        return default

    res, *rest = args
    for x in rest:
        res = x if x < res else res
    return res

>>> min()   # Теперь работает
None

>>> min(xs, default=0)  # Обязательно присваивать по имени
```

*Еще пример*

```Python
def unique(xs, seen=None):
    seen = seen or set()
    res = []
    for x in xs:
        if x in seen:
            continue
        seen.add(x)
        res.append(x)
    return res

>>> unique([1, 2, 3, 2])
[1, 2, 3]
```

Как заставить использовать только именованные ключевые аргументы где нам нужно? Поставить   `*`. Код становится более понятным. Так как стоит `*`, то интерпретатор ожидает именованных переменных.

*Пример*

```Python
def min(a, *, b=6):
    if(a < b):
        return a
    else:
        return b

>>> min(4)
4

>>> min(2, b=3)
2

>>> min(2, 3)
Traceback (most recent call last):
    File "main.py", line 8m in <module>
        min(2, 3)
TupeError: min() takes 1 positional argument but 2 where given

``` 

### `*args` и `**kwargs` ###

- `*` преобразует в аргументы список без имен.
- `**` преобразует словарь в именованные аргументы

```Python
def call_me(*args, **kwargs):
    return (args, kwargs)

>>> call_me(**{"a": 92})    # Передали в качестве аргументов пустой список и словарь
((), {'a': 92}
```

```Python
def add(x, y):
    return x+y

>>> mass = [1, 2]
>>> add(*mass)      # Аргументы в список без имен
3

>>> dict = {'x':1, 'y':2}   
>>> add(**dict)     # Словарь в именованные аргументы

>>> print(*[1], *[2], 3)
1 2 3
```

```Python
>>> dict(**{'x': 1}, y=2, **{'z':3})
{'x':1, 'y':2, 'z': 3}

>>> *range(4), 4
(0, 1, 2, 3, 4)

>>> [*range(4), 4]
[0, 1, 2, 3, 4]

>>> {*range(4), 4}
{0, 1, 2, 3, 4}

>>> {'x': 1, **{y: 2}}
{'x': 1, 'y': 2}

>>> {'x': 1, **{'x': 2}}    # При совпадении ключей, последний идет в словарь
{'x': 2}

>>> {**{'x': 2}, 'x': 1}    #
{'x': 1}
```

*Применим*

```Python
def min1(**kwargs):
    print(kwargs)
    for (key, it) in kwargs.items():
        print(key, it)

    return

>>> print(min1(**{'x':1, **{'x': 2}})
{'x':2}
x 2
None
```

Рассмотрим присвоения такого рода:

```Python
>>> x, *xs = [1, 2, 3]  ## * - это шаблон - пример получения головы и хвоста списка
>>> x, xs
(1, [2, 3])

>>> x, *xs = "123"  ## Может быть любой итерабельный тип, но при применении звездочки получается список.
>>> x, xs
('1', ['2', '3'])

>>> first, *rect, last = [1, 2, 3]
>>> first, rect, last
(1, [2], 3)

>>> rectangle = ((0, 0), (2, 3))
>>> (x1, y1), (x2, y2) = rectangle
>>> y2
3
```

Зачем нужно присвоение такого рода? Извлекаем из кортежа координаты в отдельные переменные.

Здесь могут склеится две строки и мы получим не тот результат. 

```Python
"""
name,price,quantity
spam,8,92
"""

d = {}
for line in text.splitlines():
    cells = line.split(',')
    d[cells[0]] = cells[1]
```

Исправление при условии, что строк должно быть ровно три:

```Python
"""
name,price,quantity
spam,8,92
"""

d = {}
for line in text.splitlines():
    name, price, _ = line.split(',')
    d[name] = price
```

Исправление при условии, что количество строк после первых двух не важно:

```Python
"""
name,price,quantity
spam,8,92
"""

d = {}
for line in text.splitlines():
    name, price, *_ = line.split(',')   # Все последующие параметры экранируются *
    d[name] = price
```

## Область видимости ##



















