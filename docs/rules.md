# Заголовок 1

## Заголовок 2

### Заголовок 3

**Термин** --- определение

Ненумерованный список:

-   элемент
-   элемент

Вставка изображения

![image0](images/relational_terms.png)

В конце документа реализация ссылки на изображения

Нумерованый список:

1.  раз
2.  два

Константы и ключевые слова

`SMALLINT` `NULL` `–32 768`

Таблица `PROGRAMMER`

  LANG_ID   NAME
  --------- ----------
  1         Андрей
  2         Леонид
  1         Сергей
  4         Григорий

Пример запроса

``` sql
SELECT * FROM PROGRAMMER P INNER JOIN LANG L ON P.LANG_ID = L.LANG_ID
```

::: note
::: title
Note
:::

Заметка
:::

Грамматика

``` ABNF
<ordering-item> ::=
    {col-name | col-alias | col-position | expression}
    [COLLATE collation-name]
    [ASC[ENDING] | DESC[ENDING]]
    [NULLS {FIRST | LAST}]
```
