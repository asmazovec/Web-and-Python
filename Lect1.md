# Разработка веб сервисов для научных и практических задач #


## Введение ##

### Изучение средств разработки. Python. Почему Python. ###

- Why not?

Язык подходит для быстрой реализации какого-либо проекта, осуществления какой-либо идеи, оценки ее работоспособности. Высокая динамичность языка.

- Python лаконичный.

Получается короткий код. Нет кучи фигурных скобочек с точками с запятой. 

*Java style*

```Java  
// A Java program for splitting a string
// using split()

import java.io.*;
public class Test
{
    public static void main(String args[]) 
    {
        String Str = new String("Geeks-for-Geeks");
        
        // Split above string in at-most two strings
        for (String val: Str.split("-",2))
        	System.out.println(val);
            
        System.out.println("");
		
        // Splits Str into all possible tokens
        for (String val: Str.split("-"))
        	System.out.println(val);
    }
}
```

*Python 3.x style*

```Python
line = "Geek \nGeek2 \nGeek3"
print(line.split())
print(line.split('\n'))
```

- Python легко читаемый

*Пример кода*

```Python
def magic(top):
    acc = []
    for entry in os.scandir(top):
        if entry.is_file() and entry.name.endwith(".py"):
            acc.append(entry.path)
    return acc
```

### Возможности. ###

#### Интроспекция. ####

Высокая динамичность. Можно получить и изменить данные о программном объекте во время исполнения.

Необходимые для интроспекции данные хранятся в специальных атрибутах. Так, например, получить все пользовательские атрибуты большинства объектов можно из специального атрибута - словаря (или из другого объекта, предоставляющего интерфейс `dict`) `__dict__`

*Примеры из вики*

```Python
>>> class x(object):pass
....
>>> f = x()
>>> f.attr = 12
>>> print(f.__dict__) ##### 1
{'attr': 12}
>>> print(x.__dict__) # т.к. классы тоже являются экземплярами объекта type,
                      # то они поддерживают этот тип интроспекции
{'__dict__': <attribute '__dict__' of 'x' objects>, '__module__' ........}
```

*Получение кода и байткода функции*

```Python
def f(x):pass
print(f.__code__)
print(f.__code__.co_code)
```

#### Получение интроспекции на функцию. Модуль inspect. ####

**1**

```Python
import inspect
def function(x, y - 10)
    return x**2
val = inspect.getfullargspec(function)
val1 = inspect.signature(function)
print(f'{val}')
print(f'signature {val1}')
```

*Результат*

```Python
FullArgSpec(args=['x', 'y'], varargs=None, varkw=None, defaults=(10,), kwonlyargs=[], kwonlydefaults=None, annotation={})
signature (x, y=10)
```

---

**2**

```Python
import inspect
def fun(x, y=10m *unnamed, par1=4, par2=3, **unnamed)->int:
    for val in unnamed:
        print(f'unnamed value {val}')
    for key, val in named.items():
        print(f'named value {key} = {val}')
        
    return x+y
print(inspect.getfullargspec(fun))
print(fun(2, 2, 'ul', 1, 2, par1=2, par2=2, name1=1, name2='s'))
```

*Результат*

```Python
FullArgSpec(args=['x', 'y'], varargs='unnamed', varkw='named', defaults=(10,), kwonlyargs=['par1', 'par2'], kwonlydefaults={'par1': 4, 'par2': 3}, annotations={'return':<class 'int'>})
unnamed value u1
unnamed value 1
unnamed value 2
named value name1 = 1
named value name2 = s
4
```

#### Аннотации типов ####

Необходима для анализа кода, контроля типов, несмотря на динамическую типизацию, например с помощью `mypy`.  
`python -m mypy test_type.py`

Три способа аннотации переменых +1 для функций

1. `var = value  # type: annotation`
2. `var: annotation; var = value`
3. `var: annotation = value`

*Примеры*

```Python
>>> name "John" # type: str  #### Автоматическая аннотация
>>> name: str; name = "John" #### Вручную в 2 команды
>>> name: str = "John"       #### То же самое в 1 команду
```

*+1 для функции*

4. `def func()->int`

#### Проверка типов ####

Различают типы `int`, `float`, `bool`, `str`, `complex`...

```Python
>>> type(int)
<class 'type'>

>>> type(2+2)
<class 'int'>

>>> d = (1+2j)
>>> type(d)
<class 'complex(2.0)'>
```

##### Особенности #####

- Все является объектом,
- Все является выражением (даже, например, определение функции - это выражение),
- Подерживается парадигма ООП,
- Поддерживается функциональная парадигма.

#### Обзор типов. ####

##### None. #####

Аналог NULL, но является полноценным объектом

```Python
>>> res = print(None) # Любая функция принимая None вернет None
None

>>> res == None       # Не делайте так
True

>>> res is None       # Лучше так
True

>>> id(None)           # В CPython - адрес
140503861072
```

---

##### Bool. #####

Логический тип, принимает значения `True` и `False`

На нем определены 

- операции алгебры логики `not`, `and`, `or` ..
- операции сравнения `>`, `<`, `==`, `>=`, `<=` ..

```Python
>>> to_be = False
>>> to_be or not to_be    # Получается использовать слова вместо значков
True
```

```Python
>>> x = 1
>>> y = 2
>>> x**2 + y**2 < 5 == True    # True синглтон, не делайте так
False 

>>> x**2 + y**2 < 5 is True    # И так тоже не делайте
False

>>> x**2 + y**2 < 5    # Гуд
False
```

```Python
>>> False and print('also')    # Short-circuiting!
False

>>> res = True and print('also')
also

>>> assert res is None, "print should return None"  ## Смотри пояснение 

>>> False or 92    # Работает для любого значения
92
```

*Операции*

```Python
>>> flag = True
>>> flag
True

>>> flag + True
2

>>> flag + False
1

>>> flag and False
False
```

*Пояснение*

Assert применяется для отлавливания багов, вызывая исключение, если какое-то условие было нарушено.

```Python
def apply_discount(product, discount):
    price = int(product['цена'] * (1.0 - discount))
    assert 0 <= price <= product['цена']
    return price
```

```Python
>>> apply_discount(shoes, 0.25)    # Все верно, скидка 25%
11175
>>> apply_discount(shoes, 2.0)     # Скидка в 200% вызовет ибку исполнения
Traceback (most recent call last):
    File "<input>", line 1, in <module>
        apply_discount(prod, 2.0)
    File "<input>", line 4, in apply_discount
        assert 0 <= price <= product['price']
AssertionError
```

***Truthy/Falsy***

```Python
>>> bool(True)
True

>>> bool(0)
False

>>> bool(1)
True

>>> bool([])
False

>>> bool([0])
True
```

```Python
if len(xs) == 0:    # Плохо
    pass
    
if xs:
    pass

if not xs:
    pass
```

---

##### Numbers. #####

Операции

- возведение в степень `2**10` (`== 1024`),
- деление нацело `15//2` (`== 7`),
- вещественное деление `15/2` (`== 7.5`),
- Взятие остатка `15%2` (`== 1`).

*Обратить внимание!*

```Python
>>> -3 // 2    # Как в алгебре!
-2

>>> -1 % 3     # C/Java/Rust скажут -1
2
```

*Дополнительный синтаксис.*

```Python
>>> x = 10
>>> 0 <= x and x < 100
True
 
>>> 0 <= x < 100
True
```

---

##### List. #####

Упорядоченный список элементов `[]`, `[1, 2, 'd']`

- взятие значения по индексу `some_list[0]` (+slicec)
- присвоение по индексу `some_list[0] = 'Hello, world!'`
- умножение дублирует `[1, 2] * 3 = [1, 2, 1, 2, 1, 2]` 
- взятие длины `len()` 

Опреатор

- наличие данного элемента `item in list`

```Python
>>> 1 in [1, 2, 3]
True
```

Методы

- добавить в конец `.append()`
- удалить элемент (удаляет последний по умолчанию) `.pop()` 
- создать новый список и объединить с другим `.insert(oldL, newL)`

*Обрарить внимание*

```Python
>>> xs = [[0] * 3] * 3    # Не делайте так, продублируются ссылки на изначальный список
>>> xs
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]

>>> xs[0][0] = 1    # Нежелательное изменение в 2 других местах
>>> xs
[[1, 0, 0], [1, 0, 0], [1, 0, 0]]
```

```Python
>>> xs += [1]    # Не надо так делать
>>> xs
[92, 2, 3, 1]
```

**slices**

С их помощью можно произодить выборку элементов списков

- срезает по такому правилу `item[START:STOP:STEP]`

Параметры могут опущены (хоть все), могут быть отрицательными

*Примеры*

```Python
>>> xs
[0, 1, 2, 3, 4]

>>> xs[-1]     # xs[len(xs) - 1]
4

>>> xs[2:4]    # Start Stop
[2, 3]

>>> xs[:-2]    # Stop
[0, 1, 2]

>>> xs[::2]    # Step
[0, 2, 4]

>>> xs[:]      # Defaults
[0, 1, 2, 3, 4]
```

*Важная особенность*

```Python
>>> new_list = old_list    # Ошибка! скопируется ссылка на old_list

>>> new_list = old_list[:] # Это поэлементно скопирует список
>>> id(new_list) == id(old_list)
False
```

---

##### Str. #####

Класс строки.

Операторы

- конкатенация строк `str1 + str2`
- умножение строк по аналогии со списком `str * 3` 
- наличие подстроки в строке 'substr in str'

```Python
>>> "Hello " + "world!"    # Конкатенация
'Hello world!'

>>> "multi " * 3           # Умножение
'multi multi multi '

>>> "world" in "Hello, world!"
True
```

Методы

- Разбиение по стокам `.splitlines()`
- Разбиение по словам `.split()`
- Убрать пробелы `.strip()`
- Соединить список строк через разделитель `.join()`
- Явное приведение к типу `.str()`
- Длина `.len()`

```Python
>>> "Hello \nworld".splitlines()    # По строкам
['hello ', 'world']

>>> "a, b, c".split()               # По словам
['a', 'b', 'c']

>>> ", ".join(["a", "b", "c"])      # Соединить через разделитель
'a, b, c'

>>> str(123)                        # Приведение типа
'123'

>>> len("some_string")              # Не считая символа конца строки
11
```

**Форматирование строк**  
`"%some_format" % (some_var)`

- `%` - оператор форматирования

```Python
>>> print("%d :: %s :: %d" % (flag, str, val))    # Выглядит почти по аналогии с Си
1 :: some_str :: 123
```

- можно модифицировать оператор фомата

```Python
>>> print("%8s :: %s :: %05d" % (flag, str, val))
True     :: some_str :: 00123
```

Дополнительный вариант форматирования строк - метод формат  
`"{Номер значения 1} {Номер значения 2} ...".format(значения по порядку)`

```Python
>>> s = "{} {} {}?".format("what", "is", "it")    # Автоматический порядок
>>> s
'what is it?'

>>> s = "{2} {1} {0}?".format("what", "is", "it")    # Явно указываю порядок
>>> s
'it is what?'

>>> s = "{:>7} {} {}?".format("what", "is", "it")    # Формат фнутри
'   what is it?'

>>> s = "{2:>7} {1} {0}?".format("what", "is", "it")   # Формат внутри + явное указание порядка
'     it is what?'
```

*Красивый пример*

```Python
>>> print("{0:^15}\n{1:^15}\n{2:^15}".format("*","*"*3,"*"*5))
       *       
      ***      
     *****     
```

***

Во многом работа со строками похожа на работу со списками, но в отличии от списков, строки не изменяемы. 

```Python
>>> s = "Hallo!"
>>> s[1] = "e"
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

Однако, это опять же можно обойти, используя слайсеры.

```Python
>>> s = "Hallo!"
>>> s = s[0] + 'e' + s[2:]
>>> s
'Hello!'
```

---

##### tuple #####

Класс кортежа. API, как у списка, но менять нельзя.

- пустой кортеж `()`,
- кортеж одного элемента `1` или `(1, )`,
- скобки опциональны `date = "September", 2018`

```Python
>>> date = ("September", 2018)
>>> len(date)
2

>>> date[1] = 2019
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does bot support item assignment

>>> xs = ([], []) 
>>> xs[0].extend([1, 2, 3])     # Это работает, т.к. мы не меняем саму ссылку на объект
>>> xs
([1, 2, 3], [])

>>> def div_mod(x, y):
...     return x // y, x % y    # Пример возвращения кортежа функцией. Реализация векторной функции векторного аргуменнта.

>>> d, m = div_mod(10, 3)       # присваивание кортежем d = x // y, m = x % y
>>> assert (d, m) == (3, 1)     # Ассерты для такой функции
```

---

##### set #####

Класс множества. По определению не может быть повторений и порядок элементов не важен.  
`xs = {1, 2, 3, ...}`.

Пустое множество не имеет собственного особого литерала и обозначается `set()`.


Опрераторы

- наличие элемента во множестве `in`,
- перегруженные операторы для метододов `|`, `&`, `^`

```Python
>>> xs = {1, 2, 3}
>>> 1 in xs
True

>>> 92 in xs
False
```

Методы

- добавить элемент `add()`,
- объединение множеств `|` или `.union()`,
- пересечение множеств `&` или `.intersection()`,
- исключающее или `^` или `.symmetric_difference`,
- исключение элемента из множества `.discard()`.

```Python
>>> xs = {1, 2, 3}
>>> xs.add(1)     # Уже есть
>>> xs.add(92)    
>>> xs
{1, 2, 3, 92}


>>> {1, 2, 3}.union({3, 4, 5})    # Объединение
{1, 2, 3, 4, 5}

>>> {1, 2, 3} | {3, 4, 5}
{1, 2, 3, 4, 5}


>>> {1, 2, 3}.intersection({3, 4, 5})    # Пересечение
{3}

>>> {1, 2, 3} & {3, 4, 5}
{3}


>>> {1, 2, 3}.symmetric_difference({3, 4, 5})    # XOR
{1, 2, 4, 5}

>>> {1, 2, 3} ^ {3, 4, 5}
{1, 2, 4, 5}


>>> xs.discard(2)    # В действительности операция редкая
>>> xs
{1, 3}
```

*Нельзя добавить список во множество*

```Python
>>> xs = set()
>>> xs.add([])
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

---

##### dict #####

Класс словаря.  
`dict = {"key0": value_a, "key1": value_b}`

- "длина" словаря - число входящих элементов `len()`,
- взятие значения по ключу `dict[key]`,
- присвоение по ключу `dict[key] = 14`.

```Python
>>> date = {"year": 2018, "month": "September"}

>>> len(date)    # Длина словаря
2

>>> date["year"]    # Взятие по индексу
2018

>>> date["day"] = 14    # Присвоение по индексу
>>> date["year"]
14
```

Оператор

- наличие ключа в словаре `in`.

```Python
>>> 'day' in date.keys()    # Плохо!
True

>>> 'day' in date
True
```

Методы

- писваивание значения по умолчанию `.get(key, value)`,
- удалить запись из словаря и вернуть ее значение `.pop(key)`,
- вывести все ключи `.keys()`,
- вывести все значения `.values()`,
- вывести все и сразу `.items()`.

```Python
>>> date.get("day", 14)    # Значение по умолчанию
14

>>> date.pop("year")
2018

>>> date.keys()
dict_keys(['month', 'day'])

>>> date.values()
dict_values(['September', 14])

>>> date.items()
dict_items([('month', 'September'), ('day', 14)])
```

*Упорядочение элементов*

Порядок элементов остается неизменнным и зависит только от времени присвоения ключу его первого значение. 

```Python
>>> dict_ = {}
>>> dict_["a"] = 1
>>> dict_["b"] = 2
>>> dict_["c"] = 3
>>> list(dict_.keys())
['a', 'b', 'c']

>>> dict_["a"] = 3
>>> dict_["b"] = 2
>>> dict_["c"] = 1
>>> list(dict_.keys())    # Порядок гарантируется
['a', 'b', 'c']

>>> list(dict_.values())    # Порядок гарантируется
[3, 2, 1]
```

#### Конструкции ####

##### if #####

```Python
if CONDIT1:       
    expression1()    
elif CONDIT2:
    expression2()
else:
    expression3()
```

***ternary if***

*return* **this()** `if` ***CONDIT1*** `else` **that()**

```Python
value = x if x < y else y    # int value = (x < y)? x : y; ## На Си
```

Не следует переносить условия на новую строку

```Python
# Плохо :(((
if x[0] < 100 and x[1] > 100 and (is_full_moon() or not it_thursday()) and user.is_admin
    pass
    
# Это не работает! >:(((((
if x[0] < 100 and x[1] > 100
    and (is_full_moon() or not is_thursday())
    and user.is_admin:
    pass
    
# Тоже плохо :(((
if x[0] < 100 and x[1] > 100 \
    and (is_full_moon() or not is_thursday()) \
    and user.is_admin:
    pass
    
# Со скобочками чуть лучше :((
if (x[0] < 100 and x[1] > 100
    and (is_full_moon() or not is_thursday())
    and user.is_admin):
    pass
```

##### while #####

Цикл по условию.

```Python
while CONDIT:
    expression()
```

##### for #####

`for` **var** `in` **list**`:` *expression*

```Python
for x in range(10):    # 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
    print(x)
    
for ch in "Hello world":    # H, e, l, l,o, ' ', w, o, r, l, d 
    print(ch)

```
*Забегая вперед*  
`range(START, STOP, STEP)` - генератор

##### break #####

Преждевременный принудительный выход из цикла

```Python
target = some_target
for item in items:
    if item == target:
        print("Found!", item)
        break
else:
    print("Not found")    # Особый вариант, сработает только если был break
```

##### continue #####

Принудительное завершение итерации цикла

```Python
target = some_target
res = []
for item in items:
    if item != target:
        continue
    res.append(item )
```






























