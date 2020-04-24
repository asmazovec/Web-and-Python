## Функции Python ##

### Документация ###

Зарезервированное слово def предваряет определение функции. За ним должны следовать имя функции
и заключенный в скобки список формальных параметров. Выражения, формирующие тело функции,
начинаются со следующей строки и должны иметь отступ. Первым выражением в теле функции может 
быть строковый литерал - этот литерал является строкой документации функции, или док-строкой (`docstring`)

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

**Функтор** - функция применяется к элементам, находящимся в некотором контексте, в функторе 
(список - функтор), объект к которому применима `fmap`.

```Python
X = [1, 2, 3]

>>> mp = map(lambda x:x*2, x)
>>> list(mp)
[2, 4, 6]
```

**Аппликативный функтор** - извлеченная упакованная функция (сама функция возвращает неупакованное значение),
применяется к извлеченному упакованному значению и получает упакованные значения.

**Монада** - вы применяете функцию, возвращающую упакованное значение, к упакованному значению (`PyMonad`).  
Монада - тип, который позволяет строить цепочки вычислений. Между этими вычислениями передаются
только монады, что и делает эти цепочки в некотором смысле универсальными.

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

Как заставить использовать только именованные ключевые аргументы где нам нужно? Поставить
`*`. Код становится более понятным. Так как стоит `*`, то интерпретатор ожидает именованных
 переменных.

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

Функции сработают после попытки

```Python
def is_even(n):
    return n == 0 if n <= 1 else is_odd(n - 1)

def is_odd(n):
    return n == 1 if n <= 1 else is_even(n - 1)

assert is_even(92)
```

Здесь вызов не сработает, так как `is_odd` еще не добавлена и не именована

```Python
def is_even(n):
    return n == 0 if n <= 1 else is_odd(n - 1)

assert is_even(92)  # Этот вызов не может сработать, is_odd не существует

def is_odd(n):
    return n == 1 if n <= 1 else is_even(n - 1)


==>
Traceback (most recent call last):
    File "scopes.py", line 5, in <module>
        assert is_even(92)
    File "scopes.py", line 2, in is_even
        return n == 0 if n <= 1 else is_odd(n - 1)
NameError: name 'is_odd' is not defined
```

## Словарь глобальных и локальных объектов ##

в Python есть области видимости `Global`, `Local` и `Enclosing`.

- `Local` - область видимости локальных переменных функции.
- `Enclosing` - область видимости для внутренней функции внутри функции.

Локальные переменные исчезают после вызова функции, если на них не ссылались из вложенной функции.
Функция обходится полностью сначала и преобразуется в byte code.

```Python
x = 1

def foo():
    y = 2
    print(globals(), type(globals()))
    print(locals(), type(locals()))

>>> foo()
{'x': 1, 'foo': <function ...>, ...} <class 'dict'>
{'y': 2} <class 'dict'>
```

Python сначала ищет все присвоения локальной переменной. О всех своих переменных и их
использовании знает заранее.

```Python
x = 1        # <-\
def foo():   #   |  
    print(x) # --/

>>> foo()
1
```

```Python
x = 1
def foo():
    print(x) # --\
    x = 2    # <-/

>>> foo()
UnboundLocalError:
local variable 'x' referenced before assignment
```

**Замыкание (closure)** - это функция, в теле которой присутствуют ссылки на переменные, объявленные 
вне тела этой функции в окружающем коде и не являющиеся ее параметрами.

```Python
def fun():
    x = 2
    def bar():
        print(x)
        x = 2
    bar()
    return

>>> foo()
UnboundLocalError: 
local variable 'x' referenced before assignment
```

```Python
def fun():
    x = 2
    def bar():
        print(x)
    bar()
    return

>>> fun()
2
```

```Python
def mul5(a)
    return mul(5, a)

>>> mul5(2)
10

>>> mul5(7)
35

def mul(a):
    print(a)
    def helper(b):
        print(b)
        return a*b
    return helper

print(mul(5)(2))

>>> new_mul5 = mul(5)
>>> new_mul5
<function mul.<locals>.helper at 0x000001A7548C1158>

>>> new_mul5(2)
10

>>> new_mul5(7)
35
```

Для изменений глобальных и вышестоящей функции

- `global`,
- `nonlocal`.

```Python
x = 1
def foo():
    global x
    x = 2
    y = 1
    def bar():
        nonlocal y
        y = 2
```

Создает список функций с обращением к enclosing для последнего значения и с дефолтным значением.
Примерно то же самое с многомерными массивами и `[[]*3]*3`. В первом случае для вложенной функции
глобальна, соответственно для функции берется ее последнее принятое значение.

```Python
# 1
def foo():
    res = []
    for i in range(3):
        def bar():
            return i

        res.append(bar)
    return res

for f in foo():     
    print(f(), end=" ") 

==> 2 2 2
```

```Python
# 2
def foo():
    res = []
    for i in range(3):
        def bar(i=i):
            return i

        res.append(bar)
    return res

for f in foo():     
    print(f(), end=" ") 

==> 0 1 2
```

То же для `x`, при добавлении функций для каждой вложенной функции берется последнее присвоенное
значение `x`.

```
def f():
    x = 2
    val = []
    def f1():
        return x
    val.append(f1)
    x = 9
    def f1():
        return x
    val.append(f1)
    return val
z = f()

for f in z:
    print(f)
    print(f())

==> 
<function f.<locals>.f1 at 0x7f2090499790>
9
<function f.<locals>.f1 at 0x7f2090499790>
9
```

## Лямбда выражение ##


Схема объявления и использования лямбда выражений

```Python
lambda arguments: expression    # Лямбда функция 

def _(arguments):               # Ее аналог в виде именованной функции _()
    return expression
```

*Пример описания лямбда выражения*

```Python
lambda a, *args, b=1, **kwargs: 92
```

### Функция высшего порядка map ###

```Python
>>> range(3)
range(0, 3)

>>> list(range(3))
[0, 1, 2]

>>> map(lambda x: x + 1, [0, 1, 2])
<map object at 0x7fc54d060da0>

>>> list(map(lambda x: x + 1, [0, 1, 2]))   # x соотносится и применяется лямбда функция
[3, 5, 7]

>>> list(map(lambda x: x + y, [0, 1, 2], [3, 4, 5, 6]))
[3, 5, 7]
```

### Фильтрация по условию ###

```Python
>>> list(filter(lambda x: x % 2 == 0, range(10)))
[0, 2, 4, 6, 8]

>>> list(filter(None, [0, 1, True, False, [], {None}])) # Фильтрация без функции, убивает все
Falsy элементы
[1, True, {None}]
```

### Спаривание итерируемых объектов ###

```Python
>>> list(zip("hello", range(10)))
[('h', 0), ('e', 1), ('l', 2), ('l', 3), ('o', 4)]
```

*Пример перебора упакованных вместе значений*

```Python
assert len(xs) == len(ys)
for x, y in zip(xs, ys):
    ...
```

---

*Далее два примера с идентичным результатом, но первый понятнее и короче*

```Python
>>> [x**2 for x in range(10) if x % 2 == 0]
[0, 4, 16, 36, 64]
```

```Python
>>> list(map(
            lambda x: x**2,
            filter(lambda x: x % 2 == 0, range(10))
            ))
[0, 4, 16, 36, 64]
```

---

*Аналогичный пример*

```Python
res = [
    (x, y)
    for x in range(5)
    if x % 2 == 0
    for y in range(x)
    if y % 2 == 1
]

print(res)

==> [(2, 1), (4, 1), (4, 3)]
```

```Python
res = []
for x in range(5):
    if x % 2 == 0:
        for y in range(x):
            if y % 2 == 1:
                res.append((x, y))

print(res)
```




