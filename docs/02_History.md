# История

В истории вычислительной техники можно проследить развитие двух основных
областей ее использования:

Первая область --- применение вычислительной техники для выполнения
численных расчетов, сложных алгоритмов обработки с помощью
алгоритмических языков, но все они имеют дело с простыми структурами
данных, объем которых невелик.

Вторая область --- это использование средств вычислительной техники в
автоматических или автоматизированных информационных системах.

Информационная система представляет собой программно-аппаратный
комплекс, обеспечивающий выполнение следующих функций:

-   надежное хранение информации в памяти компьютера;
-   выполнение специфических для данного приложения преобразований
    информации и вычислений;
-   предоставление пользователям удобного и легко осваиваемого
    интерфейса.

Важным шагом в развитии именно информационных систем явился переход к
использованию централизованных систем управления файлами.

**Файл** --- это именованная область внешней памяти, в которую можно
записывать и из которой можно считывать данные

Правила именования файлов, способ доступа к данным, хранящимся в файле,
и структура этих данных зависят от конкретной системы управления файлами
и, возможно, от типа файла. Система управления файлами берет на себя
распределение внешней памяти, отображение имен файлов в соответствующие
адреса во внешней памяти и обеспечение доступа к данным. Пользователь
может выполнить ряд стандартных операций:

-   создать файл (требуемого типа и размера);
-   открыть ранее созданный файл;
-   прочитать из файла некоторую запись (текущую, следующую, предыдущую,
    первую, последнюю);
-   записать в файл на место текущей записи новую, добавить новую запись
    в конец файла.

Структура записи файла была известна только программе, которая с ним
работала. Каждая программа, работающая с файлом, должна была иметь у
себя внутри структуру данных, соответствующую структуре этого файла.
Поэтому при изменении структуры файла требовалось изменять структуру
программы, а это требовало новой компиляции. То есть это означает
зависимость программ от данных. Информационные системы используются
многими пользователями одновременно. При изменении структуры файлов
необходимо изменять программы всех пользователей. А ведет дополнительные
затраты на разработку.

Это было первым существенным недостатком файловых систем, который явился
толчком к созданию новых систем хранения и управления информацией.

Так как файлы являются общим хранилищем данных, то система управления
файлами должна обеспечить авторизацию доступа к файлам. Для каждого
существующего файла указываются действия, которые разрешены или
запрещены данному пользователю. Каждому зарегистрированному пользователю
соответствует пара целочисленных идентификаторов: идентификатор группы,
к которой относится этот пользователь, и его собственный идентификатор в
группе . Для каждого файла должен храниться полный идентификатор
пользователя, который создал этот файл, и фиксироваться, какие действия
ему доступы и доступны для других пользователей группы.

Администрирование режимов доступа к файлу в основном выполняется его
создателем-владельцем. Для множества файлов, отражающих информационную
модель одной предметной области, такой децентрализованный принцип
управления доступом вызывал дополнительные трудности. Отсутствие
централизованных методов управления доступом к информации послужило еще
одной причиной разработки СУБД.

Одновременная работа нескольких пользователей во многопользовательских
ОС, связанная с модификацией данных в файле, либо вообще не
реализовывалась, либо очень замедлена.

Все эти недостатки послужили развитию нового подхода к управлению
информацией. Этот подход был реализован в СУБД (системах управления
данными). История развития СУБД насчитывает более 30 лет. В 1968 году
была введена в эксплуатацию первая промышленная СУБД система IMS фирмы
IBM. В 1975 году появился первый стандарт ассоциации по языкам систем
обработки данных --- Conference of Data System Languages (CODASYL),
который определил ряд фундаментальных понятий в теории систем баз
данных, которые и до сих пор являются основополагающими для сетевой
модели данных. В дальнейшее развитие теории баз данных большой вклад был
сделан американским математиком Э. Ф. Коддом, который является
создателем реляционной модели данных. В 1981 году Э. Ф. Кодд получил за
создание реляционной модели и реляционной алгебры престижную премию
Тьюринга Американской ассоциации по вычислительной технике.

Развитие вычислительной техники повлияло также и на развитие технологии
баз данных. Можно выделить четыре этапа в развитии данного направления в
обработке данных.

## Первый этап

Первый этап развития СУБД связан с организацией баз данных на больших
машинах типа IBM 360/370, ЕС-ЭВМ и мини-ЭВМ типа PDP11 (фирмы Digital
Equipment Corporation --- DEC), разных моделях HP (фирмы Hewlett
Packard).

Базы данных хранились во внешней памяти центральной ЭВМ, пользователями
этих баз данных были задачи, запускаемые в основном в пакетном режиме.
Интерактивный режим доступа обеспечивался с помощью консольных
терминалов, которые не обладали собственными вычислительными ресурсами
(процессором, внешней памятью) и служили только устройствами
ввода-вывода для центральной ЭВМ. Программы доступа к БД писались на
различных языках и запускались как обычные числовые программы.

Особенности этого этапа развития выражаются в следующем:

1.  Все СУБД базируются на мощных мультипрограммных операционных
    системах (MVS, SVM, RTE, OSRV, RSX, UNIX), поэтому в основном
    поддерживается работа с централизованной базой данных в режиме
    распределенного доступа.
2.  Функции управления распределением ресурсов в основном осуществляются
    операционной системой (ОС).
3.  Поддерживаются языки низкого уровня манипулирования данными,
    ориентированные на навигационные методы доступа к данным.
4.  Значительная роль отводится администрированию данных.

Проводятся серьезные работы по обоснованию и формализации реляционной
модели данных, и была создана первая система (System R), реализующая
идеологию реляционной модели данных.

Проводятся теоретические работы по оптимизации запросов и управлению
распределенным доступом к централизованной БД, было введено понятие
транзакции. Результаты научных исследований открыто обсуждаются в
печати, идет мощный поток общедоступных публикаций, касающихся всех
аспектов теории и практики баз данных, и результаты теоретических
исследований активно внедряются в коммерческие СУБД. Появляются первые
языки высокого уровня для работы с реляционной моделью данных. Однако
отсутствуют стандарты для этих первых языков.

## Второй этап

Второй этап -- это этап развития персональных компьютеров.

Особенности этого этапа следующие:

1.  Все СУБД были рассчитаны на создание БД в основном с монопольным
    доступом.
2.  Большинство СУБД имели развитый и удобный пользовательский
    интерфейс.
3.  В большинстве существовал интерактивный режим работы с БД как в
    рамках описания БД, так и в рамках проектирования запросов. Кроме
    того, большинство СУБД предлагали развитый и удобный инструментарий
    для разработки готовых приложений без программирования (на основе
    готовых шаблонов форм, конструкторов запросов).
4.  Во всех СУБД поддерживался только внешний уровень представления
    реляционной модели, то есть только внешний табличный вид структур
    данных.
5.  При наличии высокоуровневых языков манипулирования данными типа
    реляционной алгебры и SQL в настольных СУБД поддерживались
    низкоуровневые языки манипулирования данными на уровне отдельных
    строк таблиц.
6.  В настольных СУБД отсутствовали средства поддержки ссылочной и
    структурной целостности базы данных. Эти функции должны были
    выполнять приложения.

Наличие монопольного режима работы фактически привело к вырождению
функций администрирования БД и в связи с этим --- к отсутствию
инструментальных средств администрирования БД. Сравнительно скромные
требования к аппаратному обеспечению со стороны настольных СУБД.
Представители этого семейства --- очень широко использовавшиеся до
недавнего времени СУБД Dbase (DbaseIII+, DbaseIV), FoxPro, Clipper,
Paradox.

## Третий этап

Третий этап - распределенные базы данных (переход от персонализации к
интеграции).

Особенности этого этапа:

Практически все современные СУБД обеспечивают поддержку полной
реляционной модели, а именно:

1.  О структурной целостности --- допустимыми являются только данные,
    представленные в виде отношений реляционной модели;
2.  О языковой целостности, то есть языков манипулирования данными
    высокого уровня (в основном SQL);
3.  О ссылочной целостности, контроля за соблюдением ссылочной
    целостности в течение всего времени функционирования системы, и
    гарантий невозможности со стороны СУБД нарушить эти ограничения.

Большинство современных СУБД рассчитаны на многоплатформенную
архитектуру, то есть они могут работать на компьютерах с разной
архитектурой и под разными операционными системами.

Необходимость поддержки многопользовательской работы с базой данных и
возможность децентрализованного хранения данных потребовали развития
средств администрирования БД с реализацией общей концепции средств
защиты данных.

Создание теоретических трудов по оптимизации реализаций распределенных
БД и работе с распределенными транзакциями и запросами с внедрением
полученных результатов в коммерческие СУБД.

Для того чтобы не потерять клиентов, которые ранее работали на
настольных СУБД, практически все современные СУБД имеют средства
подключения клиентских приложений, разработанных с использованием
настольных СУБД, и средства экспорта данных из форматов настольных СУБД
второго этапа развития.

Разработка стандартов языков описания и манипулирования данными SQL89,
SQL92, SQL99 и технологий по обмену данными между различными СУБД.

Разработка концепцией объектно-ориентированных БД --- ООБД.
Представителями СУБД, относящимся к второму этапу, можно считать MS
Access 97 и все современные серверы баз данных Oracle7.3,Oracle 8.4 MS
SQL6.5, MS SQL7.0, System 10, System 11, Informix, DB2, SQL Base и
другие современные серверы баз данных, которых в настоящий момент
насчитывается несколько десятков.

## Четвертый этап

Четвертый этап характеризуется появлением новой технологии доступа к
данным --- интранет.

Основное отличие этого подхода от технологии клиент-сервер состоит в
том, что отпадает необходимость использования специализированного
клиентского программного обеспечения. Для работы с удаленной базой
данных используется стандартный браузер.

При этом встроенный в загружаемые пользователем HTML-страницы код,
написанный обычно на языке Java, Java-script, Perl и других, отслеживает
все действия пользователя и транслирует их в низкоуровневые SQL-запросы
к базе данных, выполняя, таким образом, ту работу, которой в технологии
клиент-сервер занимается клиентская программа. Сложные задачи
реализованы в архитектуре "клиент-сервер" с разработкой специального
клиентского программного обеспечения.
