
В предыдущих статьях описывалась работа только с одной таблицей базы данных. В реальности же очень часто приходится делать выборку из нескольких таблиц, каким-то образом объединяя их. В данной статье вы узнаете основные способы соединения таблиц.

Например, если мы хотим получить информацию о тратах на покупки, мы можем её получить следующим образом:

```sql
SELECT family_member, amount * unit_price AS price FROM Payments
```

В поле family_member полученной выборки отображаются идентификаторы записей из таблицы Payments, но для нас они мало что значат.

Вместо этих идентификаторов было бы гораздо нагляднее выводить имена тех, кто покупал (поле member_name из таблицы FamilyMember). Ровно для этого и существует объединение таблиц и оператор JOIN.
![[Pasted image 20240227152836.png]]
## Общая структура многотабличного запроса

```sql
SELECT поля_таблиц
FROM таблица_1
[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN таблица_2
    ON условие_соединения
[[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN таблица_n
    ON условие_соединения]
```

Как можно увидеть по структуре, соединение бывает:

- внутренним INNER (по умолчанию)
- внешним OUTER, при этом внешнее соединение делится на левое LEFT, правое RIGHT и полное FULL

запрос с внутренним соединением будет выглядеть следующим образом:

```sql
SELECT family_member, member_name, amount * unit_price AS price FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```
![[Pasted image 20240227153028.png]]

## Вывод всех столбцов из таблицы в многотабличном запросе

Ранее, когда все запросы выполнялись на одной таблице, было достаточно указать символ *, чтобы вывести все поля из этой таблицы. Теперь же, когда таблиц может быть несколько, * будет означать "вывести все столбцы из таблиц, перечисленных в выражении FROM".

В некоторых случаях нам может понадобиться вывести столбцы, принадлежащие только какой-то конкретной таблице. Например, дано соединение таблиц Payments и FamilyMembers, и в итоговую выборку необходимо вывести только поля из таблицы FamilyMembers. Как это сделать? Всё очень просто! Необходимо перед символом * добавить название таблицы:
```sql
SELECT FamilyMembers.* FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```
А что, если нужно вывести **несколько столбцов из одной таблицы и все из другой**? Это тоже возможно! Выведем поля payment_id и family_member из Payments, а также все поля из FamilyMembers:

```sql
SELECT payment_id, family_member, FamilyMembers.* FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

## Понятие внутреннего соединения

Внутреннее соединение — соединение, при котором находятся пары записей из двух таблиц, удовлетворяющие условию соединения, тем самым образуя новую таблицу, содержащую поля из первой и второй исходных таблиц.

Для наглядности это выглядит следующим образом:

![Схема внутреннего соединения](https://sql-academy.org/_next/image?url=%2Fstatic%2FguidePage%2Finner-join%2Finner-join-example.png&w=3840&q=50 "Схема внутреннего соединения")
## Использование WHERE для соединения таблиц
Для внутреннего соединения таблиц также можно использовать оператор WHERE. Например, вышеприведённый запрос, написанный с помощью INNER JOIN, будет выглядеть так:

```sql
SELECT family_member, member_name FROM Payments, FamilyMembers
    WHERE Payments.family_member = FamilyMembers.member_id
```

Объедините таблицы Class и Student_in_class с помощью внутреннего соединения по полям Class.id и Student_in_class.class. Выведите название класса (поле Class.name) и идентификатор ученика (поле Student_in_class.student).

```SQL
SELECT Class.name, Student_in_class.student FROM Class
INNER JOIN Student_in_class
    ON Class.id = Student_in_class.class
```
Дополните запрос из предыдущего задания, добавив ещё одно внутреннее соединение с таблицей Student. Объедините по полям Student_in_class.student и Student.id и вместо идентификатора ученика выведите его имя (поле first_name).
```SQL
SELECT Class.name, Student.first_name FROM Class
INNER JOIN Student_in_class
    ON Class.id = Student_in_class.class
INNER JOIN Student
    ON Student_in_class.student = Student.id
```

Выведите названия продуктов, которые покупал член семьи со статусом "son". Для получения выборки вам нужно объединить таблицу Payments с таблицей FamilyMembers по полям family_member и member_id, а также с таблицей Goods по полям good и good_id.
```sql
SELECT good_name FROM Goods
INNER JOIN Payments
    ON Goods.good_id = Payments.good
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
WHERE FamilyMembers.status = 'Son'
```
Выведите идентификатор (поле room_id) и среднюю оценку комнаты (поле rating, для вывода используйте псевдоним avg_score), составленную на основании отзывов из таблицы Reviews.
```SQL
SELECT Reservations.room_id, AVG(Reviews.rating) AS avg_score FROM Reviews
INNER JOIN Reservations
    ON Reviews.reservation_id = Reservations.id
GROUP BY Reservations.room_id
```

# Внешнее соединение OUTER JOIN
Внешнее соединение может быть трёх типов: левое (LEFT), правое (RIGHT) и полное (FULL). По умолчанию оно является полным.

Главным отличием внешнего соединения от внутреннего является то, что оно обязательно возвращает все строки одной (LEFT, RIGHT) или двух таблиц (FULL).

**Внешнее левое соединение (LEFT OUTER JOIN)**
Соединение, которое возвращает все значения из левой таблицы, соединённые с соответствующими значениями из правой таблицы, если они удовлетворяют условию соединения, или заменяет их на NULL в обратном случае.

Для примера получим из базы данных расписание звонков, объединённых с соответствующими занятиями в расписании занятий.
```sql
SELECT Timepair.id 'timepair.id', start_pair, end_pair,
    Schedule.id 'schedule.id', date, class, number_pair, teacher, subject, classroom
FROM Timepair
    LEFT JOIN Schedule ON Schedule.number_pair = Timepair.id;
```
В выборку попали все строки из левой таблицы, дополненные данными о занятиях. Примечательно, что в конце таблицы есть строки с полями, заполненными NULL. Это те строки, для которых не нашлось соответствующих занятий, однако они присутствуют в левой таблице, поэтому тоже были выведены.

## Внешнее правое соединение (RIGHT OUTER JOIN)]
Соединение, которое возвращает все значения из правой таблицы, соединённые с соответствующими значениями из левой таблицы, если они удовлетворяют условию соединения, или заменяет их на NULL в обратном случае.

## Внешнее полное соединение (FULL OUTER JOIN)

Соединение, которое выполняет внутреннее соединение записей и дополняет их левым внешним соединением и правым внешним соединением.

Алгоритм работы полного соединения:

- Формируется таблица на основе внутреннего соединения (INNER JOIN)
- В таблицу добавляются значения не вошедшие в результат формирования из левой таблицы (LEFT OUTER JOIN)
- В таблицу добавляются значения не вошедшие в результат формирования из правой таблицы (RIGHT OUTER JOIN)
## Базовые запросы для разных вариантов объединения таблиц

| Схема                                                                                     | Запрос с JOIN                                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/left.svg)           | Получение всех данных из левой таблицы, соединённых с соответствующими данными из правой:<br><br>```sql<br>SELECT поля_таблиц <br>FROM левая_таблица LEFT JOIN правая_таблица <br>    ON правая_таблица.ключ = левая_таблица.ключ <br>```                                                                                           |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/right.svg)          | Получение всех данных из правой таблицы, соединённых с соответствующими данными из левой:<br><br>```sql<br>SELECT поля_таблиц<br>FROM левая_таблица RIGHT JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>```                                                                                             |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/left_no_right.svg)  | Получение данных, относящихся только к левой таблице:<br><br>```sql<br>SELECT поля_таблиц<br>FROM левая_таблица LEFT JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>WHERE правая_таблица.ключ IS NULL<br>```                                                                                             |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/right_no_left.svg)  | Получение данных, относящихся только к правой таблице:<br><br>```sql<br>SELECT поля_таблиц<br>FROM левая_таблица RIGHT JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>WHERE левая_таблица.ключ IS NULL<br>```                                                                                            |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/right_and_left.svg) | Получение данных, относящихся как к левой, так и к правой таблице:<br><br>```sql<br>SELECT поля_таблиц<br>FROM левая_таблица INNER JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>```                                                                                                                    |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/all.svg)            | Получение всех данных, относящихся к левой и правой таблицам, а также их внутреннему соединению:<br><br>```sql<br>SELECT поля_таблиц <br>FROM левая_таблица<br>    FULL OUTER JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>```                                                                         |
| ![](https://sql-academy.org/static/guidePage/multi-table-request-join/right_xor_left.svg) | Получение данных, не относящихся к левой и правой таблицам одновременно (обратное INNER JOIN):<br><br>```sql<br>SELECT поля_таблиц <br>FROM левая_таблица<br>    FULL OUTER JOIN правая_таблица<br>    ON правая_таблица.ключ = левая_таблица.ключ<br>WHERE левая_таблица.ключ IS NULL<br>    OR правая_таблица.ключ IS NULL<br>``` |

Выведите имя first_name и фамилию last_name каждого учителя из таблицы Teacher, а также количество занятий, в которых он был назначен преподавателем. Если преподаватель не был назначен ни на одно занятие, то выведите 0.
```SQL
SELECT Teacher.first_name, Teacher.last_name, COUNT(Schedule.id) AS amount_classes FROM Teacher
LEFT JOIN Schedule
    ON Schedule.teacher = Teacher.id
GROUP BY Teacher.first_name, Teacher.last_name
```

