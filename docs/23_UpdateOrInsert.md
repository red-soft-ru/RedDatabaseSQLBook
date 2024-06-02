# Вставка или обновление записей

Если мы не знаем, существует ли запись, которую нужно обновить, или её
требуется добавить, удобно использовать оператор `UPDATE OR INSERT`.

Его синтаксис:

``` sql
UPDATE OR INSERT INTO target [(<column_list>)]
    VALUES (<value_list>)
    [MATCHING (<column_list>)]
    [RETURNING <returning_list> [INTO <variables>]]
```

Что именно произойдёт, вставка или обновление, зависит от значений
столбцов в предложении `MATCHING`, а в случае, если оно не указано, то
от значений столбцов первичного ключа. Если найдены записи, совпадающие
с указанными значениями, то они обновятся, а иначе будет добавлена новая
запись.

Когда у таблицы нет первичного ключа, то обязательно надо указать
`MATCHING`.

Предложение `RETURNING` используется так же как и в операторах `UPDATE`
или `INSERT`.

Пример запроса:

``` sql
UPDATE OR INSERT INTO Cows (Name, Number, Location)
    VALUES ('Suzy Creamcheese', 3278823, 'Green Pastures')
    MATCHING (Number)
```
