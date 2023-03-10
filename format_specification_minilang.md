# Форматирование строк с помощью Format Specification Mini-Language

«Спецификации формата» используются в полях замены, содержащихся в строке формата, для определения того, как представляются отдельные значения (см. [Синтаксис строки формата](https://docs.python.org/3/library/string.html#formatstrings) и [литералы форматированных строк](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)). Каждый форматируемый тип может определять, как следует интерпретировать спецификацию формата.

Спецификаторы формата можно передать непосредственно:

- В строку, при помощи форматирования в стиле СИ через разделитель `%`
- При форматировании методом `.format()`
- При форматировании f-строк

В большинстве случаев синтаксис аналогичен старому %-форматированию с добавлением {} и : вместо %. Например, «%03.2f» можно перевести как «{:03.2f}».

## Спецификация формата:

`[[fill]align][sign][#][0][width][grouping_option][.precision][type]`

Опция | Возможные значения
----|----
`fill` - **заполнитель**   |  любой символ
`align` **выравнивание** | "<" \| ">" \| "=" \| "^"
`sign` **знак**          |  "+" \| "-" \| " "
`#`                |  опция '#' заставляет использовать «альтернативную форму» для преобразования.
`0`
`width`  **ширина**      | десятичное число |
`grouping_option` **группирование**  | "_" \| ","|
`precision` **точность** |  десятичное число|
`type`   **тип**        |  "b" \| "c" \| "d" \| "e" \| "E" \| "f" \| "F" \| "g" \| "G" \| "n" \| "o" \| "s" \| "x" \| "X" \| "%"|

### `fill` заполнитель

*Если указано допустимое значение выравнивания*, ему может предшествовать символ заполнения, который может быть любым символом (по умолчанию равен пробелу, если он опущен). Невозможно использовать литеральную фигурную скобку "{" или "}" в качестве символа заполнения в f-строке или при использовании метода str.format(). Однако можно вставить фигурную скобку с вложенным замещающим полем. Это ограничение не влияет на функцию format().

### `align` выравнивание

`<` Принудительно выравнивает поле по левому краю в доступном пространстве (это значение по умолчанию для большинства объектов).

`>` Принудительно выравнивает поле по правому краю в доступном пространстве (это значение по умолчанию для чисел).

`=` Принудительно размещает заполнение после знака (если есть), но перед цифрами. Это используется для печати полей в форме «+000000120». Этот параметр выравнивания действителен только для числовых типов. Он становится значением по умолчанию для чисел, когда «0» непосредственно предшествует ширине поля.

`^` Центрирует поле в доступном пространстве.

Обратите внимание, что *если не определена минимальная ширина поля*, ширина поля всегда будет равна размеру данных для его заполнения, так что *параметр выравнивания в этом случае не имеет значения*.

``` python
'{:+<8}'.format('some')  # 'some++++'
'{:+>8}'.format('some')  # '++++some'
'{:+^8}'.format('some')  # '++some++'
'{:+=8}'.format(-5)  # '-++++++5'

```



### `sign` знак

Параметр знака действителен только для числовых типов и может быть одним из следующих:

`+` указывает, что знак должен использоваться как для положительных, так и для отрицательных чисел.

`-` указывает, что знак следует использовать только для отрицательных чисел (это поведение по умолчанию).

`_` space или пробел указывает, что перед положительными числами следует использовать начальный пробел, а перед отрицательными — знак минус.

### Альтернативное представление

`#` заставляет использовать «альтернативную форму» для преобразования.

Альтернативная форма определяется по-разному для разных типов. Этот параметр действителен только для целых, плавающих и сложных типов.

Для целых чисел, когда используется двоичный, восьмеричный или шестнадцатеричный вывод, эта опция добавляет к выходному значению соответствующий префикс '0b', '0o', '0x' или '0X'.

Для чисел с плавающей запятой и сложных альтернативная форма приводит к тому, что результат преобразования всегда содержит символ десятичной точки, даже если за ним не следуют цифры. Обычно символ десятичной точки появляется в результате этих преобразований только в том случае, если за ним следует цифра. Кроме того, для преобразований 'g' и 'G' конечные нули не удаляются из результата.

``` python
'{:b}'.format(42)  # '101010'
'{:#b}'.format(42)  # '0b101010'

'{:x}'.format(42)  # 2a
'{:#x}'.format(42)  # '0x2a'

'{:o}'.format(42)  # '52'
'{:#o}'.format(42)  # '0o52'
```


### `width` ширина
`0` или ведущие нули - Если явное выравнивание не задано, перед полем ширины ставится нулевой («0») символ, что позволяет использовать знаковое заполнение нулями для числовых типов. Это эквивалентно символу заполнения «0» с типом выравнивания «=».

В версии Python 3.10: установка «0» перед полем ширины больше не влияет на выравнивание строк по умолчанию.

`width`  ширина  -  десятичное целое число, определяющее минимальную общую ширину поля, включая любые префиксы, разделители и другие символы форматирования. Если не указано, то ширина поля будет определяться содержимым.

``` python
'{:04}'.format(5)  # '0005'
'{:04}'.format(-5)  # '-005'

# То же самое с использованием выравнивания:
'{:0=4}'.format(5)  # '0005'
'{:0=4}'.format(-5)  # '-005'
```


### `grouping_option` группировка символов

Опция ',' сигнализирует об использовании запятой для разделителя тысяч.
Для разделителя, учитывающего локаль, используйте целочисленный тип представления 'n'.

Опция '_' сигнализирует об использовании подчеркивания для разделителя тысяч для типов представления с плавающей запятой и для целочисленного типа представления 'd'.
Для целочисленных типов представления «b», «o», «x» и «X» символы подчеркивания будут вставлены через каждые 4 цифры. Для других типов презентаций указание этой опции является ошибкой.

``` python
'{:}'.format(1000000)  # '1000000'
'{:,}'.format(1000000)  # '1,000,000'
```

### `precision` точность

Точность представляет собой десятичное целое число, указывающее, сколько цифр должно отображаться после запятой для типов представления «f» и «F» или до и после десятичной запятой для типов представления «g» или «G».

Для типов строкового представления поле указывает максимальный размер поля, другими словами, сколько символов будет использовано из содержимого поля.

Точность не допускается для целочисленных типов представления.

### `type`  тип

Тип определяет способ представления данных.

Доступные типы представления строк:

`s` Формат строки. Это тип по умолчанию для строк, и его можно опустить.

`None` То же, что `s`.

Доступные типы целочисленного представления:

Тип | Значение
--|--
`b` | Двоичный формат. Выводит число по основанию 2.
`c` | Символ. Преобразует целое число в соответствующий символ Юникода перед печатью.
`d` | Десятичное целое. Выводит число по основанию 10.
`o` | Восьмеричный формат. Выводит число по основанию 8.
`x` | Шестнадцатеричный формат. Выводит число по основанию 16, используя строчные буквы для цифр выше 9.
`X` | Шестнадцатеричный формат. Выводит число по основанию 16, используя прописные буквы для цифр выше 9. В случае, если указан `#`, префикс «0x» также будет преобразован в верхний регистр до «0X».
`n` | Число. Это то же самое, что и 'd', за исключением того, что он использует текущую настройку локали для вставки соответствующих символов-разделителей чисел.
`None` | То же, что `d`.

В дополнение к указанным выше типам представления целые числа могут быть отформатированы с перечисленными ниже типами представления чисел с плавающей запятой (кроме `n` и `None`). При этом float() используется для преобразования целого числа в число с плавающей запятой перед форматированием.

Доступные типы представления для значений с плавающей запятой и десятичных чисел:

Тип | Значение
--|--
`e` | Научная нотация. Для заданной точности `p` форматирует число в экспоненте с буквой «e», отделяющей коэффициент от показателя степени. Коэффициент имеет одну цифру до и `p` цифр после запятой, всего `p + 1` значащих цифр. Без заданной точности использует точность 6 цифр после запятой для числа с плавающей запятой и показывает все цифры коэффициента для десятичного числа. Если за десятичной точкой не следуют цифры, десятичная точка также удаляется, если не используется опция `#`.
`E` | Научная нотация. То же, что и «e», за исключением того, что в качестве символа-разделителя используется буква «E» в верхнем регистре.
`f` | Обозначение с фиксированной точкой. Для заданной точности `p` форматирует число как десятичное число с ровно `p` цифрами после запятой. Без заданной точности использует точность 6 цифр после запятой для числа с плавающей запятой и использует точность, достаточную для отображения всех цифр коэффициента для десятичного числа. Если за десятичной точкой не следуют цифры, десятичная точка также удаляется, если не используется опция `#`.
`F` | Обозначение с фиксированной точкой. То же, что и «f», но преобразует nan в NAN и inf в INF.
`g` | Общий формат. Для заданной точности `p >= 1` это округляет число до `p` значащих цифр, а затем форматирует результат либо в формате с фиксированной запятой, либо в экспоненциальном представлении, в зависимости от его величины. Точность 0 считается эквивалентной точности 1. Точные правила таковы: предположим, что результат, отформатированный с типом представления `e` и точностью `p-1`, будет иметь показатель степени `exp`. Затем, если `m <= exp < p`, где `m` равно `-4` для чисел с плавающей запятой и `-6` для десятичных чисел, число форматируется с типом представления `f` и точностью `p-1-exp`. В противном случае число форматируется с типом представления `e` и точностью `p-1`. В обоих случаях из мантиссы удаляются незначащие конечные нули, а также удаляется десятичная точка, если за ней не следуют оставшиеся цифры, если только не используется опция `#`.  Без указания точности использует точность `6` значащих цифр для числа с плавающей запятой. Для класса [Decimal](https://docs.python.org/3/library/decimal.html#decimal.Decimal) коэффициент результата формируется из цифр коэффициента значения; научная запись используется для значений, меньших `1e-6` по абсолютной величине, и значений, в которых разрядное значение младшей значащей цифры больше 1, а в противном случае используется запись с фиксированной запятой. Положительная и отрицательная бесконечность, положительный и отрицательный нуль и nans форматируются как `inf`, `-inf`, `0`, `-0` и `nan` соответственно, независимо от точности.
`G` | Общий формат. То же, что и `g`, за исключением переключения на `E`, если число становится слишком большим. Представления бесконечности и NaN также в верхнем регистре.
`n` | Число. Это то же самое, что и `g`, за исключением того, что он использует текущую настройку локали для вставки соответствующих символов-разделителей чисел.
`%` | Процент. Умножает число на 100 и отображает в фиксированном формате (`f`), за которым следует знак процента.
`None` | - Для числа с плавающей запятой это то же самое, что и `g`, за исключением того, что когда для форматирования результата используется запись с фиксированной точкой, он всегда включает по крайней мере одну цифру после десятичной точки. Используемая точность настолько велика, насколько это необходимо для точного представления заданного значения. - Для `Decimal` это то же самое, что и `g` или `G`, в зависимости от значения `context.capitals` для текущего десятичного контекста.  - Общий эффект состоит в том, чтобы сопоставить вывод `str()` с изменениями, внесенными другими модификаторами формата.


## Примеры

Accessing arguments by position:
```python

>>> '{0}, {1}, {2}'.format('a', 'b', 'c')
'a, b, c'
>>> '{}, {}, {}'.format('a', 'b', 'c')  # 3.1+ only
'a, b, c'
>>> '{2}, {1}, {0}'.format('a', 'b', 'c')
'c, b, a'
>>> '{2}, {1}, {0}'.format(*'abc')      # unpacking argument sequence
'c, b, a'
>>> '{0}{1}{0}'.format('abra', 'cad')   # arguments' indices can be repeated
'abracadabra'
```

Accessing arguments by name:
```python

>>> 'Coordinates: {latitude}, {longitude}'.format(latitude='37.24N', longitude='-115.81W')
'Coordinates: 37.24N, -115.81W'
>>> coord = {'latitude': '37.24N', 'longitude': '-115.81W'}
>>> 'Coordinates: {latitude}, {longitude}'.format(**coord)
'Coordinates: 37.24N, -115.81W'
```

Accessing arguments’ attributes:
```python

>>> c = 3-5j
>>> ('The complex number {0} is formed from the real part {0.real} '
...  'and the imaginary part {0.imag}.').format(c)
'The complex number (3-5j) is formed from the real part 3.0 and the imaginary part -5.0.'
>>> class Point:
...     def __init__(self, x, y):
...         self.x, self.y = x, y
...     def __str__(self):
...         return 'Point({self.x}, {self.y})'.format(self=self)
...
>>> str(Point(4, 2))
'Point(4, 2)'
```

Accessing arguments’ items:
```python

>>> coord = (3, 5)
>>> 'X: {0[0]};  Y: {0[1]}'.format(coord)
'X: 3;  Y: 5'
```
Replacing %s and %r:
```python

>>> "repr() shows quotes: {!r}; str() doesn't: {!s}".format('test1', 'test2')
"repr() shows quotes: 'test1'; str() doesn't: test2"
```

Aligning the text and specifying a width:
```python

>>> '{:<30}'.format('left aligned')
'left aligned                  '
>>> '{:>30}'.format('right aligned')
'                 right aligned'
>>> '{:^30}'.format('centered')
'           centered           '
>>> '{:*^30}'.format('centered')  # use '*' as a fill char
'***********centered***********'
```

Replacing %+f, %-f, and % f and specifying a sign:
```python

>>> '{:+f}; {:+f}'.format(3.14, -3.14)  # show it always
'+3.140000; -3.140000'
>>> '{: f}; {: f}'.format(3.14, -3.14)  # show a space for positive numbers
' 3.140000; -3.140000'
>>> '{:-f}; {:-f}'.format(3.14, -3.14)  # show only the minus -- same as '{:f}; {:f}'
'3.140000; -3.140000'
```

Replacing %x and %o and converting the value to different bases:
```python

>>> # format also supports binary numbers
>>> "int: {0:d};  hex: {0:x};  oct: {0:o};  bin: {0:b}".format(42)
'int: 42;  hex: 2a;  oct: 52;  bin: 101010'
>>> # with 0x, 0o, or 0b as prefix:
>>> "int: {0:d};  hex: {0:#x};  oct: {0:#o};  bin: {0:#b}".format(42)
'int: 42;  hex: 0x2a;  oct: 0o52;  bin: 0b101010'
```
Using the comma as a thousands separator:
```python
>>> '{:,}'.format(1234567890)
'1,234,567,890'
```

Expressing a percentage:
```python
>>> points = 19
>>> total = 22
>>> 'Correct answers: {:.2%}'.format(points/total)
'Correct answers: 86.36%'
```

Using type-specific formatting:
```python
>>> import datetime
>>> d = datetime.datetime(2010, 7, 4, 12, 15, 58)
>>> '{:%Y-%m-%d %H:%M:%S}'.format(d)
'2010-07-04 12:15:58'
```

Nesting arguments and more complex examples:

``` python

>>> for align, text in zip('<^>', ['left', 'center', 'right']):
...     '{0:{fill}{align}16}'.format(text, fill=align, align=align)
...
'left<<<<<<<<<<<<'
'^^^^^center^^^^^'
'>>>>>>>>>>>right'
>>>
>>> octets = [192, 168, 0, 1]
>>> '{:02X}{:02X}{:02X}{:02X}'.format(*octets)
'C0A80001'
>>> int(_, 16)
3232235521
>>>
>>> width = 5
>>> for num in range(5,12):
...     for base in 'dXob':
...         print('{0:{width}{base}}'.format(num, base=base, width=width), end=' ')
...     print()
...
    5     5     5   101
    6     6     6   110
    7     7     7   111
    8     8    10  1000
    9     9    11  1001
   10     A    12  1010
   11     B    13  1011
```



## Conversion конверсия или поле преобразования

f_string          ::=  `(literal_char | "{{" | "}}" | replacement_field)*`

replacement_field ::=  `"{" f_expression ["="] ["!" conversion] [":" format_spec] "}"`

Части строки за пределами фигурных скобок обрабатываются буквально, за исключением того, что любые двойные фигурные скобки '{{' или '}}' заменяются соответствующей одинарной фигурной скобкой.

Одна открывающая фигурная скобка '{' отмечает поле замены, которое начинается с выражения Python. Чтобы отобразить как текст выражения, так и его значение после оценки (полезно при отладке), после выражения можно добавить знак равенства '='.

Когда указан знак равенства '=', на выходе будет текст выражения, '=' и вычисленное значение. Пробелы после открывающей фигурной скобки '{', внутри выражения и после '=' сохраняются в выводе. По умолчанию '=' вызывает repr() выражения, если не указан формат. Когда указан формат, по умолчанию используется str() выражения, если только не объявлено преобразование '!r'.
Конверсия или поле преобразования, представленное восклицательным знаком '!' может быть следующим:

Если указано преобразование, результат вычисления выражения преобразуется перед форматированием.

- '!s' вызывает str() для результата
- '!r' вызывает repr()
- '!a' вызывает ascii()

Спецификатор формата также может быть добавлен через двоеточие ':'. Поле замены заканчивается закрывающей фигурной скобкой '}'.

Примеры для f-строк:
``` python
>>> name = "Fred"
>>> f"He said his name is {name!r}."
"He said his name is 'Fred'."
>>> f"He said his name is {repr(name)}."  # repr() is equivalent to !r
"He said his name is 'Fred'."
>>> width = 10
>>> precision = 4
>>> value = decimal.Decimal("12.34567")
>>> f"result: {value:{width}.{precision}}"  # nested fields
'result:      12.35'
>>> today = datetime(year=2017, month=1, day=27)
>>> f"{today:%B %d, %Y}"  # using date format specifier
'January 27, 2017'
>>> f"{today=:%B %d, %Y}" # using date format specifier and debugging
'today=January 27, 2017'
>>> number = 1024
>>> f"{number:#0x}"  # using integer format specifier
'0x400'
>>> foo = "bar"
>>> f"{ foo = }" # preserves whitespace
" foo = 'bar'"
>>> line = "The mill's closed"
>>> f"{line = }"
'line = "The mill\'s closed"'
>>> f"{line = :20}"
"line = The mill's closed   "
>>> f"{line = !r:20}"
'line = "The mill\'s closed" '
```
Conversion вызывает приведение типа перед форматированием. Обычно работа по форматированию значения выполняется методом __format__() самого значения. Однако в некоторых случаях желательно принудительно форматировать тип как строку, переопределяя собственное определение форматирования. При преобразовании значения в строку перед вызовом __format__() обычная логика форматирования игнорируется.

В настоящее время поддерживаются три флага преобразования: '!s', который вызывает str() для значения, '!r', который вызывает repr(), и '!a', который вызывает ascii().

Некоторые примеры:

```python
"Harold's a clever {0!s}"  # Сначала вызывает str() для аргумента
"Bring out the holy {name!r}" # Сначала вызывает repr() для аргумента
"More {!a}" # Сначала вызывает ascii() для аргумента
```

Поле format_spec содержит спецификацию того, как должно быть представлено значение, включая такие детали, как ширина поля, выравнивание, заполнение, десятичная точность и так далее. Каждый тип значения может определять свой собственный «мини-язык форматирования» или интерпретацию format_spec.

Большинство встроенных типов поддерживают общий мини-язык форматирования, который описан в следующем разделе.

Поле format_spec также может включать в себя вложенные замещающие поля. Эти вложенные поля замены могут содержать имя поля, флаг преобразования и спецификацию формата, но более глубокая вложенность не допускается. Поля замены в format_spec заменяются перед интерпретацией строки format_spec. Это позволяет динамически задавать форматирование значения.

## Дополнительные материалы

[Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatstrings)

[A Guide to the Newer Python String Format Techniques](https://realpython.com/python-formatted-output/)

[Строковые и байтовые литералы](https://docs.python.org/3/reference/lexical_analysis.html#string-and-bytes-literals)


[%-formatting](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting)

[Formatted string literals (f-strings)](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)

[str.format](https://docs.python.org/3/library/string.html#formatstrings)

[string.Template documentation ](https://docs.python.org/3/library/string.html#template-strings)

[Formatting using locals() and globals()](https://mail.python.org/pipermail/python-ideas/2015-July/034671.html)

[PEP 3101 – Advanced String Formatting](https://peps.python.org/pep-3101/)


[Форматирование строк с помощью f-строк](https://pyneng.readthedocs.io/ru/latest/book/08_python_basic_examples/f-string.html)

[Форматирование строк с методом format](https://pyneng.readthedocs.io/ru/latest/book/04_data_structures/string_format.html)

