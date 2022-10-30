Слияние наборов данных
======================

Оператор ``MERGE`` производит слияние записей источника в целевую таблицу (или
обновляемое представление). Источником может быть таблица, представление, хранимая
процедура или производная таблица. Каждая запись источника используется для
обновления (предложение ``UPDATE``) или удаления (предложение ``DELETE``) одной или более
записей цели, или вставки (предложение ``INSERT``) записи в целевую таблицу, или ни для того,
ни для другого. Условие обычно содержит сравнение столбцов в таблицах источника и цели.

Синтаксис оператора ``MERGE``:

.. code:: sql

    MERGE INTO target [[AS] target_alias]
        USING <source> [[AS] source_alias] ON <join condition> <merge when> [<merge when> ...]
        [RETURNING <returning_list> [INTO <variable_list>]]

.. code:: bnf

    <merge when> ::= <merge when matched> | <merge when not matched>

    <merge when matched> ::= WHEN MATCHED [ AND <condition> ]
        THEN { UPDATE SET <assignment_list> | DELETE }
 
    <merge when not matched> ::= WHEN NOT MATCHED [ AND <condition> ]
        THEN INSERT [ (<column_list>) ] VALUES (<value_list>)


Допускается указывать несколько предложений ``WHEN MATCHED`` и ``WHEN NOT MATCHED``.

Предложения ``WHEN`` проверяются в указанном порядке. Если условие в предложении ``WHEN``
не выполняется, то мы пропускаем его и переходим к следующему предложению. Так будет
происходить до тех пор, пока условие для одного из предложений ``WHEN`` не будет выполнено.
В этом случае выполняется действие, связанное с предложением ``WHEN``, и осуществляется
переход на следующую строку источника. Для каждой строки источника выполняется только
одно действие.

Примеры:

.. code:: sql

    MERGE INTO books b
        USING purchases p ON p.title = b.title AND p.booktype = 'bk'
        WHEN MATCHED THEN
            UPDATE SET b.descr = b.descr || '; ' || p.descr
        WHEN NOT MATCHED THEN
            INSERT (title, descr, bought) VALUES (p.title, p.descr, p.bought);

С использованием производной таблицы.

.. code:: sql

    MERGE INTO customers c
        USING (SELECT * FROM customers_delta WHERE id > 10) cd ON (c.id = cd.id)
        WHEN MATCHED THEN
            UPDATE SET name = cd.name
        WHEN NOT MATCHED THEN
            INSERT (id, name) VALUES (cd.id, cd.name);

С использованием предложения ``DELETE``

.. code:: sql

    MERGE INTO SALARY_HISTORY
        USING (
            SELECT EMP_NO FROM EMPLOYEE WHERE DEPT_NO = 120) EMP
        ON SALARY_HISTORY.EMP_NO = EMP.EMP_NO
        WHEN MATCHED THEN DELETE

В следующем примере происходит ежедневное обновление таблицы ``PRODUCT_IVENTORY`` на
основе заказов, обработанных в таблице ``SALES_ORDER_LINE``. Если количество заказов на
продукт таково, что уровень запасов продукта опускается до нуля или становится ещё ниже, то
строка этого продукта удаляется из таблицы ``PRODUCT_IVENTORY``.

.. code:: sql

    MERGE INTO PRODUCT_IVENTORY AS TARGET
        USING (
            SELECT SL.ID_PRODUCT, SUM(SL.QUANTITY)
                FROM SALES_ORDER_LINE SL
                JOIN SALES_ORDER S ON S.ID = SL.ID_SALES_ORDER
            WHERE S.BYDATE = CURRENT_DATE
            GROUP BY 1
        ) AS SRC(ID_PRODUCT, QUANTITY)
        ON TARGET.ID_PRODUCT = SRC.ID_PRODUCT
        WHEN MATCHED AND TARGET.QUANTITY - SRC.QUANTITY <= 0 THEN
            DELETE
        WHEN MATCHED THEN
            UPDATE SET TARGET.QUANTITY = TARGET.QUANTITY - SRC.QUANTITY, TARGET.BYDATE = CURRENT_DATE

