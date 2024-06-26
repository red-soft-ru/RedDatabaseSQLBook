# Таблицы

Таблица --- наиболее важный и сложный объект реляционной базы данных. В
таблицах хранятся все обрабатываемые клиентскими программами данные базы
данных. Все строки одной таблицы имеют одинаковую структуру. Количество
строк в таблице произвольное. Таблица может содержать не менее одного
столбца. Пользовательские данные хранятся в таблицах, создаваемых
пользователем при помощи оператора `CREATE TABLE` и изменяемых
оператором `ALTER TABLE`. Системные данные (метаданные, описывающие
объекты базы данных) хранятся в системных таблицах, которые создаются
автоматически при первоначальном создании базы данных. База данных может
содержать не более `32640` пользовательских таблиц.

## Создание таблиц

Синтаксис оператора создания таблицы:

    CREATE TABLE <имя таблицы>
    [EXTERNAL [FILE] '<спецификация файла>']
    (<определение столбца> [, {<определение столбца> |
    <ограничение    таблицы>} ...])
    [SQL SECURITY {DEFINER | INVOKER}];

Имя таблицы должно быть уникальным среди имен таблиц базы данных,
хранимых процедур и представлений, описанных в этой базе данных.

Таблица может содержать, по меньшей мере, один столбец и практически
произвольное количество ограничений столбцов и ограничений таблицы.
Описания столбцов и ограничений одной таблицы заключаются в круглые
скобки и отделяются друг от друга запятыми.

Необязательное предложение `SQL SECURITY {DEFINER | INVOKER}`
определяет, в контексте какого пользователя будет проходить работа с
таблицей. Ключевое слово `INVOKER` (значение по умолчанию) указывает,
что таблица вызывается с правами текущего пользователя. Задание
ключевого слова `DEFINER` означает, что таблица вызывается с правами к
объектам базы данных ее владельца (создателя). Значение по умолчанию на
уровне всей базы данных можно изменить оператором
`ALTER DATABASE SET DEFAULT SQL SECURITY`.

Создать новую таблицу может администратор и пользователь с привилегией
`CREATE TABLE`. Пользователь, создавший таблицу, становится её
владельцем.

### Определение обычного столбца таблицы

Синтаксис описания столбца таблицы:

    <определение обычного столбца> ::=
        <имя столбца> { <тип данных> | <имя домена>}
        [DEFAULT {<литерал> | NULL | <контекстная переменная>}]
        [NOT NULL]
        [<ограничение столбца>]
        [COLLATE <порядок сортировки>]

Столбец должен иметь имя, уникальное только в данной таблице. В других
таблицах могут присутствовать столбцы с тем же именем.

Для столбца должен быть задан либо тип данных, либо имя домена,
характеристики которого будут скопированы в этот столбец.

Если при создании столбца таблицы осуществляется ссылка на домен,
содержащий предложение `CHECK`, а при описании столбца также задается
ограничение `CHECK`, то в результате для столбца будет сформировано
условие, являющееся конъюнкцией (логическое И) обоих условий.

Если при описании столбца таблицы не задается базовый домен, а
указывается тип данных, то для такого столбца система все равно создаст
системный домен с уникальным именем, поместив в него все описанные для
столбца характеристики.

Необязательное предложение `DEFAULT` определяет значение по умолчанию
для столбца --- это то значение, которое будет присвоено столбцу, если
при добавлении новой строки в таблицу в операторе `INSERT` не указан
данный столбец и его значение. Значение по умолчанию применяется только
при выполнении оператора добавления данных `INSERT` и не оказывает
никакого влияния на выполнение оператора изменения существующих в
таблице данных (`UPDATE`). Если в операторе изменения данных не указан
какой-либо столбец, то его значение просто не изменяется.

Значением по умолчанию может быть литерал, пустое значение `NULL` или
контекстная переменная. Если значение по умолчанию в операторе описания
столбца явно не устанавливается, то подразумевается пустое значение
`NULL`. Использование каких-либо выражений в значении по умолчанию
недопустимо.

Например. Следующий столбец типа `DATE` имеет значением по умолчанию
дату на серверном компьютере в то время, когда выполняется данный
оператор `INSERT`:

    ...
    DATE_C DATE DEFAULT CURRENT_DATE, 
    ...

Если пользователь при помещении новой строки в эту таблицу не задаст
значение даты, то система поместит туда текущую дату с сервера.

Необязательное предложение `NOT NULL` указывает, что столбцу не может
быть присвоено пустое значение в операторе `INSERT` или `UPDATE`. Это
предложение является обязательным для столбца, входящего в состав
первичного ключа таблицы.

Например. В справочной таблице стран в нашем примере код страны является
первичным ключом. По этой причине он объявлен с предложением `NOT NULL`:

    CODCOUNTRY CHAR(3) NOT NULL, /* Код страны */
    ...
    CONSTRAINT PK_COUNTRY PRIMARY KEY (CODCOUNTRY),
    ...

Ограничение столбца записывается сразу после определения других
характеристик столбца. Существует четыре вида ограничений столбца:

-   первичный ключ (`PRIMARY KEY`),
-   уникальный ключ (`UNIQUE`),
-   внешний ключ (`REFERENCES`),
-   ограничение значения столбца (`CHECK`).

Синтаксис ограничения столбца:

    <ограничение столбца> ::=
        [CONSTRAINT <имя ограничения>]
        {
          UNIQUE [<предложение USING>]
        | PRIMARY KEY [<предложение USING>]
        | REFERENCES <имя таблицы> [(<имя столбца>)] [<предложение USING>]
            [ON DELETE { NO ACTION | CASCADE | SET DEFAULT | SET NULL }]
            [ON UPDATE { NO ACTION | CASCADE | SET DEFAULT | SET NULL }]
        | CHECK (<условие столбца>)
        }
    <предложение USING> ::= USING [ASC[ENDING] | 
        DESC[ENDING]] INDEX <имя индекса>

Необязательное предложение `CONSTRAINT` задает имя ограничения столбца.
Если имя не указано, система присваивает ограничению системное имя,
например, `INTEG_28`. Рекомендуется задавать осмысленные имена
ограничениям столбца. В дальнейшем при изменении характеристик таблицы к
таким ограничениям будет проще обращаться по заданному имени. Имя
ограничения должно быть уникальным среди имен всех ограничений столбцов
и/или имен ограничений таблиц во всех таблицах базы данных, а также
среди имен созданных пользователем индексов.

Предложение `USING` позволяет задать имя индекса для поддержания
соответствующего ограничения первичного, уникального или внешнего ключа
и указать его упорядоченность --- по возрастанию значений реквизитов
ключа (`ASCENDING`) или по убыванию их значений (`DESCENDING`). Если
упорядоченность не задана, то предполагается `ASCENDING`, по
возрастанию. Если индекс не указан (не задано предложение `USING`), то
автоматически будет создан индекс с именем этого ограничения, если
указано имя ограничения, или с системным именем, если не было задано
имени ограничения в предложении `CONSTRAINT`.

Ограничение `UNIQUE` определяет уникальный ключ. При помещении в таблицу
новой строки или при изменении существующей, значение столбца, с таким
ограничением, должно быть уникальным в таблице, т.е. не должно
существовать другой строки, с таким же значением уникального поля.
Исключением из этого правила является только случай, когда уникальный
ключ имеет значение `NULL`. Строк со значением уникального ключа равным
`NULL` в таблице может быть произвольное количество.

Ограничение `PRIMARY KEY` определяет первичный ключ. В отличие от
уникального ключа в таблице может быть только один первичный ключ.

Первичный ключ является уникальным --- в таблице не может существовать
двух разных строк с одним и тем же значением первичного ключа.

Столбец, являющийся первичным ключом, должен быть описан с указанием
`NOT NULL` --- он не может иметь пустое значение.

Для уникального и первичного ключа система автоматически строит
соответствующий индекс. Если в описании уникального ключа было указано
имя ограничения в предложении `CONSTRAINT`, то это имя будет присвоено
индексу (если в предложении `USING` не было задано другого имени), иначе
индекс получит системное имя.

Ограничение `REFERENCES` определяет внешний ключ. Внешний ключ должен
иметь пустое значение NULL или же он должен ссылаться на первичный или
уникальный ключ другой или той же самой таблицы. Понятие «ссылается»
означает только лишь, что в родительской таблице должна присутствовать
строка, имеющая такое же значение первичного или уникального ключа, что
и внешний ключ дочерней таблицы.

Первичный или уникальный ключ часто называют *родительскими ключами*.

В предложении `REFERENCES` указывается имя таблицы (главной,
родительской), на первичный/уникальный ключ которой ссылается внешний
ключ подчиненной, дочерней, таблицы, и имя первичного/уникального ключа
в главной, родительской, таблице, на которую ссылается соответствующий
ключ дочерней таблицы.

Предложение `ON DELETE` определяет, что произойдет с записями
подчиненной, дочерней, таблицы при удалении соответствующей строки
главной, родительской, таблицы:

-   `NO ACTION` --- не будет выполнено никаких действий. Будет выдано
    сообщение об ошибке. Клиентское приложение должно самостоятельно
    исправить ситуацию и повторить попытку.
-   `CASCADE` --- в дочерней таблице должны быть автоматически удалены
    все записи, имеющие те же значения внешнего ключа, что и значение
    первичного (уникального) родительского ключа удаляемой строки
    родительской таблицы.
-   `SET DEFAULT` --- столбец внешнего ключа всех соответствующих строк
    в дочерней таблице устанавливается в значение по умолчанию,
    определенное в предложении `DEFAULT` этого столбца, описанного как
    внешний ключ. В подобной ситуации, как правило, в клиентской
    программе следует предпринять дополнительные меры по обеспечению
    непротиворечивости данных. Если значение по умолчанию для столбца
    внешнего ключа не задано, то столбцу присваивается значение `NULL`.
-   `SET NULL` --- значения внешнего ключа всех соответствующих строк в
    дочерней таблице устанавливаются в пустое значение `NULL`. Это не
    приведет к нарушению целостности данных, так как для внешнего ключа
    допустимо пустое значение.

Для внешнего ключа система также автоматически строит индекс. Если в
описании внешнего ключа было указано имя ограничения в предложении
`CONSTRAINT`, то это имя будет присвоено автоматически создаваемому
индексу. Рекомендуется каждому такому ограничению явно присваивать имя.

Предложение `USING` позволяет задать иное имя индекса для поддержания
ограничения внешнего ключа.

Органичение `CHECK` для столбца аналогично домену.

При создании таблицы происходит неявная нумерация столбцов. Первый
создаваемый столбец получает номер один, следующий --- номер два и т.д.
Вообще говоря, порядок столбцов в таблице особого значения не имеет за
исключением случая, когда в операторе добавления данных `INSERT` не
задан явно список столбцов. Тем не менее, для любого столбца таблицы
можно изменить номер --- переместить его с одной позиции на другую.

### Определение вычисляемого столбца

Вычисляемый столбец задается предложением COMPUTED BY.

    COMPUTED [BY] (<выражение>)

Другой вариант задания вычисляемого столбца:

    GENERATED ALWAYS AS (<выражение>)

Значение такого столбца не хранится в таблице, а вычисляется, при
выборке данных из таблицы. Термин «вычисляемый» не обязательно означает
только лишь арифметическое вычисление. Для строковых данных, например,
может применяться операция конкатенации, вызов функции получения
подстроки и ряда других встроенных функций.

Выражение в этом предложении --- выражение, возвращающее ровно одно
значение любого типа данных, кроме `BLOB` или массива. Выражение может
содержать любые допустимые операции, обращение к встроенным функциям
и/или к функциям, определенным пользователем, `UDF`. Среди значений
выражения допустимо использование и оператора `SELECT`, заключенного в
круглые скобки, который при обращении к таблице (это может быть другая
или та же самая таблица), представлению или хранимой процедуре выбора
возвращает единственное значение или `NULL`. Операндами используемых в
выражении операторов и функций могут быть различные константы,
контекстные переменные и имена столбцов этой же таблицы. Все столбцы,
используемые в выражении, должны быть определены ранее в этой таблице.
Все таблицы, представления и хранимые процедуры, к которым обращаются
операторы `SELECT`, должны уже существовать в базе данных. По этой
причине вычисляемые столбцы обычно описывают в самом конце таблицы после
ограничений таблицы или непосредственно перед ними. Еще один способ
задания вычисляемых столбцов --- добавление их в уже существующую
таблицу при помощи оператора `ALTER TABLE`, когда все таблицы,
представления и хранимые процедуры базы данных уже описаны в системе.

Вычисляемому столбцу система присваивает соответствующий тип данных,
рассчитанный, исходя из вида операций и характеристик операндов в
выражении вычисления.

**Пример 1**. Пусть в таблице существует столбец «оклад человека»,
`SALARY`. Можно создать вычисляемый столбец с именем NET_SALARY, который
будет иметь значение на 13% меньше, чем оклад (вычеты из заработной
платы).

    CREATE TABLE STAFF ( ...
         SALARY DECIMAL(8, 2),
    NET_SALARY COMPUTED BY (SALARY * 0.87) )

Вычисляемому столбцу `NET_SALARY` системой будет присвоен тип данных
`NUMERIC(18, 4)`. При выборке данных из этой таблицы оператором `SELECT`
будет возвращаться и значение вычисляемого столбца, на 13 процентов
меньшее, чем указанный оклад.

**Пример 2**. Пусть в базе данных существует справочная таблица,
содержащая сведения о странах:

    CREATE TABLE COUNTRY (
       CODCOUNTRY CHAR(3) NOT NULL,     /* Код страны */
       NAME CHAR(30),                   /* Краткое название страны */
       FULLNAME CHAR(60),               /* Полное название страны */
       CAPITAL CHAR(15),                /* Название столицы */
       DESCR BLOB,                      /* Дополнительное описание */
    CONSTRAINT PK_COUNTRY PRIMARY KEY (CODCOUNTRY) )

Другая таблица, описывающая различные организации, содержит код страны,
в которой располагается (зарегистрирована) данная организация.

    CREATE TABLE FIRM (
        COD INTEGER NOT NULL,
        NAME1 CHAR(50),
        CODCOUNTRY CHAR(3),
    ...
        COUNTRYNAME COMPUTED BY ((SELECT NAME
            FROM COUNTRY
            WHERE COUNTRY.CODCOUNTRY = FIRM.CODCOUNTRY)), 
        FULLCOUNTRYNAME COMPUTED BY ((SELECT FULLNAME
            FROM COUNTRY
            WHERE COUNTRY.CODCOUNTRY = FIRM.CODCOUNTRY))
    )

В этой таблице присутствует два вычисляемых столбца. Один получит тип
данных `VARCHAR(30)`, поскольку отыскиваемый при использовании оператора
`SELECT` столбец из справочной таблицы (краткое название страны) имеет
тип данных `VARCHAR(30)`, другой вычисляемый столбец, отыскиваемый также
в таблице стран, получает тип данных `VARCHAR(60)`. Обратите внимание,
что оператор `SELECT` заключен в двойную пару круглых скобок. Во всех
синтаксических конструкциях, где присутствует одиночный оператор
`SELECT` (оператор, возвращающий ровно одно значение одного столбца или
пустое значение `NULL`), этот оператор должен быть заключен в круглые
скобки. Внешняя пара скобок требуется, потому что выражение для любого
вычисляемого столбца по правилам синтаксиса также должно заключаться в
круглые скобки.

В обоих операторах `SELECT` в предложениях `WHERE` именам столбцов
предшествует имя соответствующей таблицы и точка. Это так называемые
уточненные имена. Имя таблицы здесь требуется, чтобы устранить
возникающую неопределенность, поскольку столбец с именем `CODCOUNTRY`
присутствует в обеих таблицах --- и в `FIRM`, и в `COUNTRY`. Для
уточненных имен возможно использование и псевдонимов (или алиасов,
alias) таблиц. Использование псевдонимов может несколько сократить
количество символов, набираемых для выполнения оператора, однако их
применение имеет больший смысл, когда в сложном запросе одна и та же
таблица встречается в нескольких различных конструкциях оператора
`SELECT`. Если для таблицы задан псевдоним, то во всех уточненных именах
столбцов можно использовать только псевдонимы, использование имени
таблицы в этом случае недопустимо. При отсутствии псевдонима
используется имя таблицы. Для главной таблицы, таблицы самого верхнего
уровня, используемой в первом операторе `SELECT`, уточняющее имя можно
не указывать.

Например, последний вычисляемый столбец этого же примера мог бы быть
записан в следующем виде:

    ...
      FULLCOUNTRYNAME COMPUTED BY ((SELECT FULLNAME
                                  FROM COUNTRY C
                WHERE C.CODCOUNTRY = FIRM.CODCOUNTRY))

Здесь для таблицы `COUNTRY` задается псевдоним `C`. После этого в любом
предложении данного оператора обращаться к данной таблице можно
**только** по псевдониму, а не по имени таблицы.

Поскольку таблица `COUNTRY` является главной таблицей в операторе
`SELECT`, то ее имя или псевдоним можно в операторе не указывать.
Последнее определение вычисляемого столбца без каких-либо ошибок можно
записать и в следующем виде:

    FULLCOUNTRYNAME COMPUTED BY ((SELECT FULLNAME
                                FROM COUNTRY
                WHERE CODCOUNTRY = FIRM.CODCOUNTRY))

Для таблицы же `FIRM` псевдоним или имя таблицы (в данном случае, именно
имя этой таблицы) обязательно должно быть указано.

### Определение столбца идентификации

Столбцы идентификации могут быть определены с помощью предложения

    GENERATED BY DEFAULT AS IDENTITY

Столбец идентификации представляет собой столбец, связанный с внутренним
генератором последовательностей. Его значение устанавливается
автоматически каждый раз, когда оно не указано в операторе `INSERT`.
Необязательное предложение `START WITH` позволяет указать начальное
значение отличное от нуля. Идентификационные столбцы неявно являются
`NOT NULL` столбцами.

Тип данных столбца идентификации должен быть целым числом с нулевым
масштабом. Допустимыми типами являются SMALLINT, INTEGER, BIGINT,
NUMERIC(x,0) и DECIMAL(x,0).

Идентификационный столбец не может иметь значений по умолчанию и
вычисляемых значений, а также не может быть изменён в обычный столбец. И
наоборот.

### Определение ограничений таблицы

Ограничения таблицы являются более универсальным, удобным и наглядным
способом описания ограничений таблицы, чем ограничения столбца. Они
применяются не только к одному столбцу, но и к группе столбцов
создаваемой (изменяемой) таблицы. Ограничение таблицы описывается
следующим синтаксисом:

    <ограничение таблицы> ::= [CONSTRAINT <имя ограничения>] {
          PRIMARY KEY (<имя столбца> [, <имя столбца> ...]) [<предложение USING>]
        | UNIQUE (<имя столбца> [, <имя столбца> ...]) [<предложение USING>]
        | FOREIGN KEY (<имя столбца> [, <имя столбца> ...])
        REFERENCES <имя таблицы> (<имя столбца> 
            [, <имя столбца> ...]) [<предложение USING>]
            [ON DELETE { NO ACTION | CASCADE | SET DEFAULT | SET NULL }]
            [ON UPDATE { NO ACTION | CASCADE | SET DEFAULT | SET NULL }]
        | CHECK (<условие столбца>)
    }
    <предложение USING> ::= USING [ASC[ENDING] | DESC[ENDING]] INDEX <имя индекса>

Ограничение таблицы, в отличие от ограничения столбца, может относиться
как к одному отдельному столбцу, так и к группе столбцов этой таблицы.
Столбцы, задаваемые в ограничении, должны быть уже описаны при создании
или изменении таблицы. По этой причине ограничения таблицы обычно
размещаются в самом конце описания таблицы или впоследствии добавляются
к описанию таблицы при использовании оператора ALTER TABLE.

Ограничению таблицы также можно присвоить имя, используя предложение
`CONSTRAINT`. Имя ограничения должно быть уникальным среди имен всех
ограничений всех таблиц, ограничений столбцов таблиц и имен всех
индексов базы данных. Если не указано предложение `USING`, то
соответствующий данному ограничению индекс получит имя создаваемого
ограничения. Иначе будет присвоено системное имя.

В случае задания предложения `USING` при описании первичного,
уникального или внешнего ключа можно указать имя создаваемого индекса,
поддерживающего соответствующее ограничение, а также и его
упорядоченность --- по возрастанию (`ASCENDING` --- по умолчанию) или по
убыванию (`DESCENDING`).

Предложение `PRIMARY KEY` задает ограничение первичного ключа. В состав
первичного ключа в ограничении таблицы может входить один или более
столбцов данной таблицы. Имена столбцов перечисляются в круглых скобках.
Каждый столбец первичного ключа должен быть явно описан с указанием
атрибута `NOT NULL`. Таблица может иметь не более одного первичного
ключа.

Для первичного ключа система автоматически создает индекс в момент
создания таблицы или при добавлении в существующую таблицу ограничения
первичного ключа в операторе `ALTER TABLE`.

Предложение `UNIQUE` задает ограничение уникального ключа. В отличие от
первичного ключа отдельные столбцы уникального ключа могут иметь пустое
значение `NULL`. При этом комбинация значений столбцов, входящих в
состав уникального ключа, должна оставаться уникальной, исходя из того,
что в подобной ситуации два пустых значения `NULL` считаются
одинаковыми. Исключением является лишь случай, когда все столбцы
уникального ключа имеют пустое значение. Таких строк с полностью
«пустым» значением уникального ключа в одной таблице может быть
произвольное количество.

Пусть, например, уникальный ключ таблицы состоит из двух столбцов K1 и
K2, и для них не указано предложение `NOT NULL`. Тогда присутствие в
такой таблице следующих строк является допустимым:

    K1      K2
    =====   =====
    1       1
    1       2
    NULL    NULL
    NULL    NULL
    1       NULL
    NULL    2
    NULL    NULL

Однако попытка записать в эту таблицу еще и любую из следующих строк
приведет к нарушению уникальности значения ключа:

    K1      K2
    =====   =====
    1       NULL
    NULL    2

Эти значения уникального ключа уже присутствуют в таблице. Такие
действия вызовут исключение базы данных, строки в таблицу помещены не
будут.

Таблица может иметь произвольное количество уникальных ключей.

Для уникального ключа система автоматически создает индекс.

Предложение `FOREIGN KEY` задает ограничение внешнего ключа. Синтаксис
этого ограничения на уровне таблицы несколько отличается от синтаксиса
определения внешнего ключа на уровне столбца. После ключевых слов
`FOREIGN KEY` в скобках указывается список столбцов создаваемой
(изменяемой) таблицы, которые включены в состав внешнего ключа. Может
быть задан и один единственный столбец. Все столбцы, входящие в состав
внешнего ключа, должны быть описаны в таблице ранее.

После ключевого слова `REFERENCES` указывается имя родительской таблицы,
на первичный или уникальный ключ которой ссылается описываемый внешний
ключ. Список имен столбцов родительской таблицы, входящих в состав
первичного (уникального) ключа помещается сразу после имени таблицы в
этом предложении и заключается в круглые скобки. Сама родительская
таблица уже должна быть создана в базе данных. Если происходит ссылка
именно на первичный ключ родительской таблицы, то список столбцов,
включенных в состав первичного ключа этой таблицы, можно не указывать.

Для внешнего ключа система автоматически создает индекс.

Структура внешнего ключа дочерней таблицы по количеству столбцов и по
типам данных, включая размерность символьных данных, должна полностью
соответствовать структуре первичного (уникального) ключа родительской
таблицы, на который ссылается данный внешний ключ. Совпадения имен не
требуется.

Необязательные предложения `ON DELETE` и `ON UPDATE` определяют, что
будет происходить с дочерней таблицей, соответственно, при удалении
строки родительской таблицы или при изменении ключевых данных в
родительской таблице. Возможные опции аналогичны соответствующему
параметру ограничения столбца, рассмотренному ранее.

Ограничение `CHECK` задает условия, которым должны удовлетворять
значения столбцов данной таблицы при помещении в таблицу новой строки
(оператор `INSERT`) или при изменении (оператор `UPDATE`) отдельных
столбцов существующей строки таблицы. Варианты и правила использования
условий таблицы полностью совпадают с условиями `CHECK` столбца, только
вместо `VALUE` можно использовать имена столбцов.

## Изменение таблицы

Описание существующих в базе данных таблиц (характеристик столбцов, их
порядка, наличие различных ключей или ограничения `CHECK`) можно
изменять после создания таблиц.

Изменение структуры таблиц, уже заполненных данными, является одним из
наиболее опасных действий, которое часто приводит к исключениям базы
данных или к потере существующих в таблице данных. Для изменения
структуры существующих таблиц используется оператор `ALTER TABLE`.
Изменять таблицу может ее владелец, администратор и пользователь с ролью
`ALTER ANY TABLE`.

Синтаксис оператора `ALTER TABLE`:

    ALTER TABLE <имя таблицы> <операция изменения> [, <операция изменения>...]

В одном операторе можно выполнить произвольное количество изменений в
таблице. Различные операции по изменению существующей таблицы отделяются
друг от друга запятыми.

Синтаксис такой операции изменения существующей таблицы:

    <операция изменения> ::= { ADD <определение столбца> 
        | ADD <ограничение таблицы>
        | DROP <имя столбца>
        | DROP CONSTRAINT <ограничение столбца или таблицы>
        | ALTER [COLUMN] <имя столбца> <модификация столбца>
        | ALTER SQL SECURITY {DEFINER|INVOKER}
        | DROP SQL SECURITY }

Некоторые изменения структуры таблицы увеличивают *счётчик форматов*,
закреплённый за каждой таблицей. Количество форматов для каждой таблицы
ограничено значением 255. После того, как счётчик форматов достигнет
этого значения, вы не сможете больше менять структуру таблицы. Для
сброса счётчика форматов необходимо сделать резервное копирование и
восстановление базы данных.

При добавлении нового столбца или ограничения в существующую в базе
данных таблицу используются уже рассмотренные конструкции
`определение стоблца` и `ограничение таблицы`.

Операция удаления столбцов (`DROP <имя стоблца>`) требует определенной
осторожности. Прежде чем удалять столбец, нужно удалить все зависимости
в базе данных, связанные с этим столбцом. Такие зависимости могут
присутствовать:

-   в ограничениях столбцов и таблицы. Такие ограничения могут
    существовать как в текущей, корректируемой, таблице, так и в любой
    другой существующей таблице базы данных. В первую очередь это могут
    быть ограничения первичного, уникального или внешнего ключа, в
    состав которого входит удаляемый столбец. Затем это могут быть
    ограничения внешних ключей в других таблицах, которые ссылаются на
    первичный или уникальный ключ корректируемой таблицы, если удаляемый
    столбец входит в состав первичного (уникального) ключа. Наконец, это
    могут быть ограничения CHECK данной или иных таблиц, в условиях
    которых присутствует удаляемый столбец.
-   в индексах, когда удаляемый столбец входит в состав каких-либо
    индексов базы данных, созданных пользователем для изменяемой
    таблицы.
-   в хранимых процедурах, функциях и триггерах, где присутствуют
    обращения к значениям удаляемого столбца.
-   в представлениях, где удаляемый столбец может присутствовать в
    списке выбора, а также в предложении `ON` соединяемых таблиц,
    определяющем условие соединения, в предложении `WHERE`, определяющем
    условие выборки, или в предложениях `ORDER BY`, существующих в
    представлениях, задающих упорядоченность результата выборки данных.

Часть `DROP CONSTRAINT` удаляет существующее ограничение столбца или
ограничение таблицы.

Часть `ALTER [COLUMN]` позволяет изменить характеристики существующих
столбцов и похожа на изменение домена. Рассмотрим как изменять разные
типы столбцов.

Необязательное предложение `ALTER SQL SECURITY {DEFINER|INVOKER}`
определяет, в контексте какого пользователя будет проходить работа с
таблицей. Ключевое слово `INVOKER` (значение по умолчанию) указывает,
что таблица вызывается с правами текущего пользователя. Задание
ключевого слова `DEFINER` означает, что таблица вызывается с правами к
объектам базы данных ее владель- ца (создателя). Значение по умолчанию
на уровне всей базы данных можно изменить оператором
`ALTER DATABASE SET DEFAULT SQL SECURITY`.

Предложение `DROP SQL SECURITY` удаляет эту опцию, указанную при
создании.

### Изменение столбца таблицы

Синтаксис соответствующей части команты `ALTER TABLE` выглядит следующим
образом:

    ALTER [COLUMN] <имя столбца> 
        | TO <новое имя столбца>
        | POSITION <новая позиция>
        | TYPE { <тип данных> | <имя домена> }
        | SET DEFAULT { <литерал> | NULL | <контекстная переменная>}
        | DROP DEFAULT
        | SET NOT NULL
        | DROP NOT NULL

Невозможно изменение имени столбца, если этот столбец включен в
какое-либо ограничение --- первичный или уникальный ключ, внешний ключ,
ограничение столбца или ограничение таблицы `CHECK`. Имя столбца также
нельзя изменить, если этот столбец таблицы используется в каком-либо
триггере, в хранимой процедуре или в представлении, созданных
пользователем. Если для таблицы был автоматически создан триггер для
поддержания какого-либо ограничения, то соответствующее имя в триггере
будет автоматически изменено при изменении имени столбца.

Нельзя изменить тип данных у столбца, который принимает участие в связке
внешний ключ / первичный (уникальный) ключ. В остальных случаях
изменение типа данных возможно, однако следует помнить, что это может
привести к большим сложностям при дальнейшей эксплуатации базы данных,
если перед таким изменением таблица была заполнена данными.

При таком изменении таблица будет содержать все существовавшие на момент
изменения типа данных строки в одной структуре, а вновь добавляемые
строки будут иметь иную структуру.

При изменении позиции столбца нужно помнить про случаи, когда существуют
операторы `INSERT`, в которых явно не указан список имен добавляемых
столбцов. При изменении позиции столбца такие операторы станут работать
неправильно. Также это может привести к неверному выполнению оператора
`SELECT`, если в списке выбора был указан выбор всех столбцов таблицы
(символ `*`) , а в предложении `ORDER BY` был задан номер столбца, по
которому выполняется упорядочивание полученных данных. При разработке
приложений такие конструкции лучше не использовать.

*Номер позиции* --- это положительное число. Столбцы в таблицах
нумеруются, начиная с позиции *один*. Если указать номер позиции,
превышающий количество столбцов в таблице, то никакие изменения в
таблице выполнены не будут, исключений базы данных не возникнет. При
задании же числа меньше единицы будет выдано сообщение об ошибке, такое
изменение не будет выполнено.

При удалении значения по умолчанию для столбца, доменное значение
перекроет удаляемое, если задан домен.

Если удаление значения по умолчанию производится над столбцом, у
которого нет значения по умолчанию, или чьё значение по умолчанию
основано на домене, то это приведёт к ошибке выполнения данного
оператора.

При добавлении значения по умолчанию, если столбец уже имел значение по
умолчанию, то оно будет заменено новым. Значение по умолчанию для
столбца всегда перекрывает доменное значение по умолчанию.

Предложение `SET NOT NULL` добавляет ограничение `NOT NULL` для столбца
таблицы. Успешное добавление ограничения `NOT NULL` происходит, только
после полной проверки данных таблицы, для того чтобы убедится что
столбец не содержит значений `NULL`.

Явное ограничение `NOT NULL` на столбце, базирующегося на домене,
преобладает над установками домена. В этом случае изменение домена для
допустимости значения `NULL`, не распространяется на столбец таблицы.

Предложение `DROP NOT NULL` удаляет ограничение `NOT NULL` для столбца
таблицы. Если столбец основан на домене с ограничением `NOT NULL`, то
ограничение домена перекроет это удаление.

Для вычисляемых столбцов допустимо изменить тип и выражение вычисляемого
столбца

    ALTER [COLUMN] <имя столбца>
        [TYPE <тип данных>]
        {GENERATED ALWAYS AS | COMPUTED [BY]} (<выражение>)

Невозможно изменить обычный столбец на вычисляемый и наоборот.

Для столбцов идентификации позволено изменять начальное значение. Если
указано только предложение `RESTART`, то происходит сброс значения
генератора в ноль. Необязательное предложение `WITH` позволяет указать
для нового значения внутреннего генератора отличное от нуля значение.
Невозможно изменить обычный столбец на столбец идентификации и наоборот.

## Удаление таблицы

Для удаления существующей таблицы используется оператор `DROP TABLE.`
Удалять таблицу может ее владелец, администратор и пользователь с
привилегией `DROP ANY TABLE`.

Синтаксис оператора:

    DROP TABLE <имя таблицы>

Нельзя удалить таблицу, которая является родительской в связке внешний
ключ / первичный (уникальный) ключ. Нельзя также удалить таблицу, на
которую существуют ссылки в триггерах (за исключением триггеров,
написанных пользователем именно для этой таблицы), и таблицу, которая
используется в хранимой процедуре или в представлении.

Таблица, используемая в какой-либо активной транзакции, не будет удалена
до завершения (подтверждения или отмены) этой транзакции.

При успешном удалении таблицы автоматически будут удалены все ее данные,
триггеры, созданные для этой таблицы (пользователем или автоматически
системой), а также все индексы, построенные автоматически системой
управления базами данных или пользователем для такой таблицы.
