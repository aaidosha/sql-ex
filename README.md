# Решения заданий с sql-ex.ru

## Краткая информация о базе данных "Компьютерная фирма":
Схема БД состоит из четырех таблиц:
 - Product(maker, model, type)
 - PC(code, model, speed, ram, hd, cd, price)
 - Laptop(code, model, speed, ram, hd, price, screen)
 - Printer(code, model, color, type, price)

Таблица Product представляет производителя (maker), номер модели (model) и тип ('PC' - ПК, 'Laptop' - ПК-блокнот или 'Printer' - принтер). Предполагается, что номера моделей в таблице Product уникальны для всех производителей и типов продуктов. В таблице PC для каждого ПК, однозначно определяемого уникальным кодом – code, указаны модель – model (внешний ключ к таблице Product), скорость - speed (процессора в мегагерцах), объем памяти - ram (в мегабайтах), размер диска - hd (в гигабайтах), скорость считывающего устройства - cd (например, '4x') и цена - price. Таблица Laptop аналогична таблице РС за исключением того, что вместо скорости CD содержит размер экрана -screen (в дюймах). В таблице Printer для каждой модели принтера указывается, является ли он цветным - color ('y', если цветной), тип принтера - type (лазерный – 'Laser', струйный – 'Jet' или матричный – 'Matrix') и цена - price.

### Задание 1
Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол. Вывести: model, speed и hd

```sql
SELECT model,
       speed,
       hd
FROM pc
WHERE price < 500
```

### Задание 2
Найдите производителей принтеров. Вывести: maker
```sql
SELECT DISTINCT maker
FROM product
WHERE type = 'Printer'
```
### Задание 3
Найдите номер модели, объем памяти и размеры экранов ПК-блокнотов, цена которых превышает 1000 дол.
```sql
SELECT model,
       ram,
       screen
FROM laptop
WHERE price > 1000
```
### Задание 4
Найдите все записи таблицы Printer для цветных принтеров.
```sql
SELECT * 
FROM printer
WHERE color = 'y'
```
### Задание 5
Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.
```sql
SELECT model,
       speed,
       hd
FROM pc
WHERE (cd = '12x'
       OR cd = '24x')
  AND price < 600
```
### Задание 6
Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ПК-блокнотов. Вывод: производитель, скорость.
```sql
SELECT DISTINCT maker,
                speed
FROM product
JOIN laptop ON product.model = laptop.model
WHERE hd >= 10
```
### Задание 7
Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).
```sql
SELECT a.model,
       price
FROM
  (SELECT model,
          price
   FROM PC
   UNION SELECT model,
                price
   FROM Laptop
   UNION SELECT model,
                price
   FROM Printer) AS a
JOIN Product p ON a.model = p.model
WHERE p.maker = 'B';
```
### Задание 8
Найдите производителя, выпускающего ПК, но не ПК-блокноты.
```sql
SELECT DISTINCT maker
FROM Product
WHERE TYPE = 'PC'
  AND maker NOT IN
    (SELECT maker
     FROM Product
     WHERE TYPE = 'Laptop')
```
### Задание 9
Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker
```sql
SELECT DISTINCT maker
FROM product
JOIN pc ON product.model = pc.model
WHERE pc.speed >= 450
```
### Задание 10
Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price
```sql
SELECT model,
       price
FROM printer
WHERE price =
    (SELECT max(price) AS maxprice
     FROM printer)
```
### Задание 11
Найдите среднюю скорость ПК.
```sql
SELECT avg(speed) as avg_speed
FROM pc
```
### Задание 12
Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол.
```sql
SELECT avg(speed) as avg_speed
FROM laptop
WHERE price > 1000
```
### Задание 13
Найдите среднюю скорость ПК, выпущенных производителем A.
```sql
SELECT avg(speed) AS avg_speed
FROM pc
WHERE model IN
    (SELECT model
     FROM product
     WHERE maker = 'A')
```
### Задание 14
Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий.
```sql
SELECT ships.class,
       name,
       country
FROM ships
JOIN classes ON ships.class = classes.class
WHERE numguns >= 10
```
### Задание 15
Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD
```sql
SELECT hd
FROM pc
GROUP BY hd
HAVING count(hd) >= 2
```
### Задание 16
Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i), Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.
```sql
SELECT DISTINCT a.model,
                b.model,
                a.speed,
                a.ram
FROM pc AS a,
     pc AS b
WHERE a.model > b.model
  AND a.ram = b.ram
  AND a.speed = b.speed
```
### Задание 17
Найдите модели ПК-блокнотов, скорость которых меньше скорости любого из ПК.
Вывести: type, model, speed
```sql
SELECT DISTINCT TYPE,
                laptop.model,
                speed
FROM product
JOIN laptop ON product.model = laptop.model
WHERE speed < all
    (SELECT speed
     FROM pc)
```
### Задание 18
Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price
```sql
SELECT DISTINCT maker,
                minprice AS price
FROM
  (SELECT min(price) AS minprice
   FROM printer
   WHERE color='y') a
JOIN printer b ON a.minprice=b.price
JOIN product ON b.model = product.model
WHERE b.color='y'
```
### Задание 19
Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.
Вывести: maker, средний размер экрана.
```sql
SELECT maker,
       avg(screen) AS avgscreen
FROM product
JOIN laptop ON product.model = laptop.model
GROUP BY maker
```
### Задание 20
Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК.
```sql
SELECT maker,
       count(model)
FROM product
WHERE product.type = 'pc'
GROUP BY maker
HAVING count(model) >= 3
```
### Задание 21
Найдите максимальную цену ПК, выпускаемых каждым производителем, у которого есть модели в таблице PC.
Вывести: maker, максимальная цена.
```sql
SELECT maker,
       max(price) AS maxprice
FROM product
JOIN pc ON product.model = pc.model
GROUP BY maker
```
### Задание 22
Для каждого значения скорости ПК, превышающего 600 МГц, определите среднюю цену ПК с такой же скоростью. Вывести: speed, средняя цена.
```sql
SELECT speed,
       avg(price) AS avgprice
FROM pc
WHERE speed > 600
GROUP BY speed
```
### Задание 23
Найдите производителей, которые производили бы как ПК
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
Вывести: Maker
```sql
SELECT DISTINCT maker
FROM product
WHERE maker IN
    (SELECT maker
     FROM product
     INNER JOIN pc ON product.model=pc.model
     WHERE speed>=750)
  AND maker IN
    (SELECT maker
     FROM product
     INNER JOIN laptop ON product.model=laptop.model
     WHERE speed>=750) )
```
### Задание 26
Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква). Вывести: одна общая средняя цена.
```sql
SELECT AVG(price)
FROM
  (SELECT price
   FROM PC
   WHERE model IN
       (SELECT model
        FROM product
        WHERE maker='A'
          AND TYPE='PC' )
   UNION ALL SELECT price
   FROM Laptop
   WHERE model IN
       (SELECT model
        FROM product
        WHERE maker='A'
          AND TYPE='Laptop' ) ) AS p
```
### Задание 28
Используя таблицу Product, определить количество производителей, выпускающих по одной модели.
```sql
WITH results AS
  (SELECT count(DISTINCT model) AS qty,
          maker
   FROM product
   GROUP BY maker)
SELECT count(maker)
FROM results
WHERE qty = 1
```
## Краткая информация о базе данных "Корабли":
Рассматривается БД кораблей, участвовавших во второй мировой войне. Имеются следующие отношения:
- Classes (class, type, country, numGuns, bore, displacement)
 - Ships (name, class, launched)
 - Battles (name, date)
 - Outcomes (ship, battle, result)

Корабли в «классах» построены по одному и тому же проекту, и классу присваивается либо имя первого корабля, построенного по данному проекту, либо названию класса дается имя проекта, которое не совпадает ни с одним из кораблей в БД. Корабль, давший название классу, называется головным.
Отношение Classes содержит имя класса, тип (bb для боевого (линейного) корабля или bc для боевого крейсера), страну, в которой построен корабль, число главных орудий, калибр орудий (диаметр ствола орудия в дюймах) и водоизмещение ( вес в тоннах). В отношении Ships записаны название корабля, имя его класса и год спуска на воду. В отношение Battles включены название и дата битвы, в которой участвовали корабли, а в отношении Outcomes – результат участия данного корабля в битве (потоплен-sunk, поврежден - damaged или невредим - OK).
Замечания. 1) В отношение Outcomes могут входить корабли, отсутствующие в отношении Ships. 2) Потопленный корабль в последующих битвах участия не принимает.

### Задание 31
Для классов кораблей, калибр орудий которых не менее 16 дюймов, укажите класс и страну.
```sql
SELECT class,
  country
FROM classes
WHERE bore >=16
```
### Задание 33
Укажите корабли, потопленные в сражениях в Северной Атлантике (North Atlantic). Вывод: ship.
```sql
SELECT ship
FROM outcomes
WHERE RESULT = 'sunk'
  AND battle = 'North Atlantic'
```
### Задание 35
В таблице Product найти модели, которые состоят только из цифр или только из латинских букв (A-Z, без учета регистра).
Вывод: номер модели, тип модели.
```sql
SELECT DISTINCT model,
                TYPE
FROM product
WHERE model NOT LIKE '%[^0-9]%'
  OR model NOT LIKE '%[^a-z]%'
```
### Задание 36
Перечислите названия головных кораблей, имеющихся в базе данных (учесть корабли в Outcomes).
```sql
SELECT DISTINCT name
FROM ships,
     classes
WHERE ships.class = ships.name
UNION
SELECT DISTINCT ship
FROM (outcomes
      LEFT OUTER JOIN ships ON outcomes.ship=ships.name)
INNER JOIN classes ON outcomes.ship=classes.class
```
### Задание 38
Найдите страны, имевшие когда-либо классы обычных боевых кораблей ('bb') и имевшие когда-либо классы крейсеров ('bc').
```sql
SELECT country
FROM classes
WHERE TYPE = 'bb' INTERSECT
  SELECT country
  FROM classes WHERE TYPE = 'bc'
```
### Задание 42
Найдите названия кораблей, потопленных в сражениях, и название сражения, в котором они были потоплены.
```sql
SELECT ship,
       battle
FROM outcomes
WHERE RESULT = 'sunk'
```
### Задание 44
Найдите названия всех кораблей в базе данных, начинающихся с буквы R.
```sql
SELECT DISTINCT name
FROM ships
WHERE name LIKE 'R%'
UNION
SELECT ship
FROM outcomes
WHERE ship LIKE 'R%'
```
### Задание 45
Найдите названия всех кораблей в базе данных, состоящие из трех и более слов (например, King George V).
Считать, что слова в названиях разделяются единичными пробелами, и нет концевых пробелов.
```sql
SELECT DISTINCT name
FROM ships
WHERE name LIKE '% % %'
UNION
SELECT ship
FROM outcomes
WHERE ship LIKE '% % %'
```
### Задание 47
Определить страны, которые потеряли в сражениях все свои корабли.
```sql
WITH T1 AS
  (SELECT COUNT(name) AS co,
          country
   FROM
     (SELECT name,
             country
      FROM Classes
      INNER JOIN Ships ON Ships.class = Classes.class
      UNION SELECT ship,
                   country
      FROM Classes
      INNER JOIN Outcomes ON Outcomes.ship = Classes.class) FR1
   GROUP BY country),
     T2 AS
  (SELECT COUNT(name) AS co,
          country
   FROM
     (SELECT name,
             country
      FROM Classes
      INNER JOIN Ships ON Ships.class = Classes.class
      WHERE name IN
          (SELECT DISTINCT ship
           FROM Outcomes
           WHERE RESULT LIKE 'sunk')
      UNION SELECT ship,
                   country
      FROM Classes
      INNER JOIN Outcomes ON Outcomes.ship = Classes.class
      WHERE ship IN
          (SELECT DISTINCT ship
           FROM Outcomes
           WHERE RESULT LIKE 'sunk') ) FR2
   GROUP BY country)
SELECT T1.country
FROM T1
INNER JOIN T2 ON T1.co = t2.co
AND t1.country = t2.country
```
### Задание 49
Найдите названия кораблей с орудиями калибра 16 дюймов (учесть корабли из таблицы Outcomes).
```sql
SELECT DISTINCT name
FROM ships
JOIN classes ON ships.class = classes.class
WHERE bore = 16
UNION
SELECT ship
FROM outcomes,
     classes
WHERE outcomes.ship = classes.class
  AND bore =16
```
### Задание 50
Найдите сражения, в которых участвовали корабли класса Kongo из таблицы Ships.
```sql
SELECT DISTINCT battles.name
FROM battles
JOIN outcomes ON battles.name = outcomes.battle
JOIN ships ON outcomes.ship = ships.name
WHERE ships.class = 'Kongo'
```
### Задание 53
Определите среднее число орудий для классов линейных кораблей.
Получить результат с точностью до 2-х десятичных знаков.
```sql
SELECT cast(avg(numguns*1.0) AS numeric(4, 2)) AS Avg_numGuns
FROM classes
WHERE type = 'bb'
```
