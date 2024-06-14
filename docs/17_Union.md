# Объединение

Предложение `UNION` объединяет два и более набора данных, тем самым
увеличивая общее количество строк, но не столбцов. Наборы данных,
принимающие участие в UNION, должны иметь одинаковое количество
столбцов. Однако столбцы в соответствующих позициях не обязаны иметь
один и тот же тип данных, они могут быть абсолютно не связанными. По
умолчанию, объединение подавляет дубликаты строк. `UNION ALL` отображает
все строки, включая дубликаты. Необязательное ключевое слово `DISTINCT`
делает поведение по умолчанию явным.

Синтаксис

``` ABNF
<union> ::=
    <individual-select>
    UNION [DISTINCT | ALL]
    <individual-select>
    [UNION [DISTINCT | ALL]
    <individual-select>
    [...]
    [<union-wide-clauses>]

    <individual-select> ::=
        SELECT
        [FIRST m] [SKIP n]
        [DISTINCT | ALL] <columns>
        FROM source [[AS] alias]

    [<joins>]
        [WHERE <condition>]
        [GROUP BY <grouping-list>
        [HAVING <aggregate-condition>]]
        [PLAN <plan-expr>]
        <union-wide-clauses> ::=
        [ORDER BY <ordering-list>]
        [ROWS m [TO n]]
        [FOR UPDATE [OF <forupdate-columns>]]
        [WITH LOCK]
        [INTO <PSQL-varlist>]
```

Объединения получают имена столбцов из первого запроса на выборку. Если
вы хотите дать псевдонимы объединяемым столбцам, то делайте это для
списка столбцов в самом верхнем запросе на выборку. Псевдонимы в других
участвующих в объединении выборках разрешены, и могут быть даже
полезными, но они не будут распространяться на уровне объединения. Если
объединение имеет предложение `ORDER BY`, то единственно возможными
элементами сортировки являются целочисленные литералы, указывающие на
позиции столбцов, необязательно сопровождаемые `ASC/DESC` и/или
`NULLS FIRST/LAST` директивами. Это так же означает, что вы не можете
упорядочить объединение ничем, что не является столбцом объединения.
(Однако вы можете завернуть его в производную таблицу, которая даст вам
все обычные параметры сортировки.) Объединения позволены в подзапросах
любого вида и могут самостоятельно содержать подзапросы. Они также могут
содержать соединения (joins), и могут принимать участие в соединениях,
если завёрнуты в производную таблицу.

Этот запрос представляет информацию из различных музыкальных коллекций в
одном наборе данных с помощью объединений:

``` sql
SELECT id, title, artist, len, 'CD' AS medium
FROM cds
UNION
SELECT id, title, artist, len, 'LP'
FROM records
UNION
SELECT id, title, artist, len, 'MC'
FROM cassettes
ORDER BY 3, 2 -- artist, title
```

Следующий запрос получает имена и телефонные номера переводчиков и
корректоров. Те переводчики, которые также работают корректорами, будут
отображены только один раз в результирующем наборе, если номера их
телефонов одинаковые в обеих таблицах. Тот же результат может быть
получен без ключевого слова `DISTINCT`. Если вместо ключевого слова
`DISTINCT`, будет указано ключевое слово ALL, эти люди будут отображены
дважды.

``` sql
SELECT name, phone
FROM translators
UNION DISTINCT
SELECT name, telephone
FROM proofreaders 
```

Пример использования UNION в подзапросе:

``` sql
SELECT name, phone, hourly_rate
FROM clowns
WHERE hourly_rate < ALL
(SELECT hourly_rate FROM jugglers
UNION
SELECT hourly_rate FROM acrobats)
ORDER BY hourly_rate
```