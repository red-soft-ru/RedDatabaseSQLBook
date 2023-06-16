Удаление записей
================

Для удаления записей из таблицы используется оператор ``DELETE``.

Его синтаксис:

.. code:: sql

    DELETE FROM target [[AS] alias]
        [WHERE {search-conditions | CURRENT OF cursorname}]
        [ORDER BY sort_items]
        [ROWS m [TO n]]
        [RETURNING <returning_list> [INTO <variables>]]

Фильтрация
----------

Условие в предложении ``WHERE`` ограничивает набор удаляемых строк. Удаляются только те
строки, которые удовлетворяют условию поиска, или только текущей строке именованного
курсора.
Удаление с помощью ``WHERE`` ``CURRENT OF`` называется позиционированным удалением, 
потому что удаляется запись в текущей позиции. Удаление при помощи
"``WHERE`` условие" называется поисковым удалением поскольку СУБД ищет
записи, соответствующие условию.

Например,

.. code:: sql

    DELETE FROM People WHERE first_name <> 'Boris' AND last_name <> 'Johnson';

    DELETE FROM employee e WHERE NOT EXISTS(
        SELECT * FROM employee_project ep WHERE e.emp_no = ep.emp_no);

    DELETE FROM Cities WHERE CURRENT OF Cur_Cities; -- только в PSQL

Сортировка и ограничения
------------------------

Предложение ``ORDER BY`` упорядочивает набор перед его удалением. Что может быть важно
в некоторых случаях.

Предложение ``ROWS`` позволяет ограничить количество удаляемых строк. Имеет смысл только
в комбинации с предложением ``ORDER BY``, но допустимо и без него.

Примеры:

Удаление самой старой покупки

.. code:: sql

    DELETE FROM Purchases ORDER BY ByDate ROWS 1

Удаление заказов для 10 клиентов с самыми большими номерами

.. code:: sql

    DELETE FROM Sales ORDER BY custno DESC ROWS 1 TO 10

Удаляет все записи из sales, поскольку не указано ``ROWS``

.. code:: sql

    DELETE FROM Sales ORDER BY custno DESC

Удаляет одну запись "с конца", т.е. от Z…

.. code:: sql

    DELETE FROM popgroups ORDER BY name DESC ROWS 1

Удаляет пять самых старых групп

.. code:: sql

    DELETE FROM popgroups ORDER BY formed ROWS 5

Сортировка (`ORDER BY`) не указана, поэтому будут удалены 8 обнаруженных
записей, начиная с пятой.

.. code:: sql

    DELETE FROM popgroups ROWS 5 TO 12

RETURNING
---------

Оператор ``DELETE``, удаляющий не более одной строки, может содержать конструкцию
``RETURNING`` для возвращения значений удаляемой строки. В ``RETURNING`` могут быть указаны
любые столбцы, не обязательно все, а также другие столбцы и выражения.

Примеры:

.. code:: sql

    DELETE FROM Scholars WHERE first_name = 'Henry' AND last_name = 'Higgins'
        RETURNING last_name, fullname, id;

.. code:: sql

    DELETE FROM Dumbbells ORDER BY iq DESC ROWS 1 RETURNING last_name, iq INTO :lname, :iq;

.. code:: sql

    DELETE FRMO TempSales ts WHERE ts.id = tempid RETURNING ts.qty INTO new.qty;