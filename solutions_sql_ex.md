# sql-ex.ru
solutions for tasks at sql-ex.ru

join - таблицы, связанные между собой
union - объединение данных из двух таблиц без каких-либо отношений
intersect - пересечение(упр 38) - данные должны быть и в 1ой, и во 2ой таблице

use web;
-- упр 1
-- Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол. Вывести: model, speed и hd
select model, speed, hd from pc where price < 500

-- упр 2
-- Найдите производителей принтеров. Вывести: maker.
select distinct maker from product where type = 'printer'

-- упр 3
-- Найдите номер модели, объем памяти и размеры экранов ПК-блокнотов, цена которых превышает 1000 дол.
select model, ram, screen from laptop where price > 1000

-- упр 4
-- Найдите все записи таблицы Printer для цветных принтеров.
select * from printer where color = 'y'

-- упр 5
-- Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.
select model, speed, hd from pc where price < 600 and (cd = '12x' or cd='24x')

-- упр 6
-- Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, 
-- найти скорости таких ПК-блокнотов. Вывод: производитель, скорость.
select distinct p.maker, l.speed from laptop l join product p on l.model = p.model
where hd >= 10

-- упр 7
-- Найдите номера моделей и цены всех имеющихся в продаже 
-- продуктов (любого типа) производителя B (латинская буква).
select pr.maker, lp.price from laptop lp join product pr on pr.model = lp.model
where pr.maker = 'B'
union
select pr.maker, pc.price from pc join product pr on pr.model = pc.model
where pr.maker = 'B'
union
select pr.maker, pri.price from printer pri join product pr on pr.model = pri.model
where pr.maker = 'B'

-- другое решение упр 7 основанное на left join
select a.model from
(((product a LEFT JOIN pc b ON a.model = b.model
) LEFT JOIN  laptop c ON a.model = c.model
) LEFT JOIN printer d ON a.model = d.model)

-- упр 8
-- Найдите производителя, продающего ПК, но не ноутбук
select distinct maker from product where type = 'pc' and 
maker not in (select maker from product where type = 'laptop')

-- упр 9
-- Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker
select distinct maker from product p join pc on p.model = pc.model  where pc.speed >= 450 

-- упр 10
-- Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price
select pri.model , price from printer pri, product pro
where 
price = (select MAX(distinct price) from printer)
and pri.model = pro.model

-- упр 11 
-- Найдите среднюю скорость ПК
select avg(speed) from PC group by price 

-- упр 12
-- Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол.
select avg(speed) from laptop where price > 1000

-- упр 13
-- Найдите среднюю скорость ПК, выпущенных производителем A
select avg(speed) from pc join product p on p.model = pc.model where p.maker = 'A'

-- упр 14
-- Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий
use ships;
select distinct c.class, name, country from classes c join ships s on c.class = s.class where numGuns >= 10

-- упр 15 Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD
use web;
select hd from pc group by hd HAVING COUNT(*)>1

-- упр 16 
-- Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, 
-- т.е. (i,j), но не (j,i), Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.
select distinct pc1.model, pc2.model, pc1.speed, pc1.ram from pc pc1, pc pc2 
where pc1.ram = pc2.ram 
and pc1.speed = pc2.speed 
and pc1.model > pc2.model

-- упр 17 
-- Найдите модели ПК-блокнотов, скорость которых меньше скорости каждого из ПК. Вывести: type, model, speed
select distinct p.type, l.model, l.speed  from laptop l join product p on l.model = p.model
where l.speed < (select min(speed) from pc)

-- упр 18 
-- Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price

-- var 1
select distinct p.maker, pr.price from printer pr join product p on pr.model = p.model
where color = 'y'
order by pr.price limit 1

-- var 2
select distinct p.maker, pr.price from printer pr join product p on pr.model = p.model
where  pr.price = (select min(price) from printer where color = 'y') and color = 'y'

-- упр 19 
-- Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.
-- Вывести: maker, средний размер экрана.
select maker, avg(screen) from laptop l join product p on l.model = p.model group by maker

-- упр 20 
-- Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК.
select maker, count(*) from product where type = 'PC' group by maker having count(*) > 2

-- упр 21 
-- Найдите максимальную цену ПК, выпускаемых каждым производителем, у которого есть модели в таблице PC.
-- Вывести: maker, максимальная цена.
select maker, max(price) from pc join product p on pc.model = p.model group by maker

-- упр 
-- 22 Для каждого значения скорости ПК, превышающего 600 МГц, определите среднюю цену ПК с такой же скоростью. 
-- Вывести: speed, средняя цена.
select speed, avg(price) from pc where speed > 600 group by speed

-- упр 23 
-- Найдите производителей, которые производили бы как ПК со скоростью не менее 750 МГц, 
-- так и ПК-блокноты со скоростью не менее 750 МГц. Вывести: Maker
select maker from pc join product p on p.model = pc.model where speed >= 750
INTERSECT
select maker from laptop l join product p on p.model = l.model where speed >= 750

-- упр 24 
-- Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции.
WITH tmp(model, price) AS (
select model, price from pc
union all
select model, price from laptop
union all
select model, price from printer)
select distinct model from tmp where price = (select max(price) from tmp)

-- упр 25 
-- Найдите производителей принтеров, которые производят ПК с наименьшим объемом RAM и с самым быстрым процессором среди всех ПК, 
-- имеющих наименьший объем RAM. Вывести: Maker
select distinct maker from pc 
join product p on pc.model = p.model 
where 
pc.ram = (select min(ram) from pc) 
and
pc.speed = (select max(speed) from pc where ram = (select min(ram) from pc))
and 
maker in (select maker from product where type = 'printer')

-- упр 26 
-- Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква). Вывести: одна общая средняя цена.
with tmp(price) AS (
select price from pc join product p on pc.model = p.model where maker = 'A'
UNION ALL
select price from laptop l join product p on l.model = p.model where maker = 'A'
)
select avg(price) from tmp

-- упр 27 
-- Найдите средний размер диска ПК каждого из тех производителей, которые выпускают и принтеры. Вывести: maker, средний размер HD.
select maker, avg(hd) from pc join product p on p.model = pc.model
where maker in (select maker from product where type = 'printer')
group by maker

-- упр 28 
-- Используя таблицу Product, определить количество производителей, выпускающих по одной модели.
with tmp(maker) as (
select maker from product p group by maker having count(*) = 1
)
select count(*) from tmp

-- упр 29 
-- В предположении, что приход и расход денег на каждом пункте приема фиксируется не чаще одного раза в день [т.е. первичный ключ (пункт, дата)], 
-- написать запрос с выходными данными (пункт, дата, приход, расход). Использовать таблицы Income_o и Outcome_o.
select i.point as point, i.date, i.inc, o.out from income_o i left join outcome_o o on i.point = o.point and i.date = o.date  
UNION
select o.point as point, o.date, i.inc, o.out from income_o i right join outcome_o o on i.point = o.point and i.date = o.date 

-- упр 30 
-- В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз (первичным ключом в таблицах является 
-- столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату выполнения операций будет соответствовать одна строка.
-- Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc). Отсутствующие значения считать неопределенными (NULL).

select i.point, i.date, o.outc, i.inc from
(select date, point, sum(inc) as inc from income group by date, point)  i
left join
(select date, point, sum(outcome.out) as outc from outcome  group by date, point)  o
on i.date = o.date and i.point = o.point
UNION
select o.point, o.date, o.outc, i.inc  from
(select date, point, sum(inc) as inc from income group by date, point)  i
right join
(select date, point, sum(outcome.out) as outc from outcome  group by date, point)  o
on i.date = o.date and i.point = o.point

решение с форума:SELECT point, date, sum(out) AS outcome, sum(inc) AS income
FROM (
    SELECT code, point, date, inc, NULL AS out FROM Income
    UNION
    SELECT code, point, date, NULL AS inc, out FROM Outcome
) AS A
GROUP BY point, date

-- упр 31 
-- Для классов кораблей, калибр орудий которых не менее 16 дюймов, укажите класс и страну.
use ships;
select class, country from classes where bore >= 16


-- упр 32 
-- НЕ СДЕЛАНО
-- Одной из характеристик корабля является половина куба калибра его главных орудий (mw). 
-- С точностью до 2 десятичных знаков определите среднее значение mw для кораблей каждой страны, у которой есть корабли в базе данных.
-- select *, avg(cast(bore*bore*bore/2 as decimal(10,2))) as mv from classes group by country
select country, cast(avg(cast(bore*bore*bore/2 as decimal(10,2)))  as decimal(10,2)) as weight from classes group by country
select * from classes
select *, cast(bore*bore*bore/2 as decimal(10,2)) from classes

-- упр 33
-- Укажите корабли, потопленные в сражениях в Северной Атлантике (North Atlantic). Вывод: ship.
select distinct ship from outcomes where result = 'sunk' and battle = 'North Atlantic'

-- упр 34
-- По Вашингтонскому международному договору от начала 1922 г. запрещалось строить линейные корабли водоизмещением более 35 тыс.тонн. 
-- Укажите корабли, нарушившие этот договор (учитывать только корабли c известным годом спуска на воду). Вывести названия кораблей.
select name from ships where launched >= 1922 and class in
(select class from classes where type = 'bb' and displacement > 35000)

-- упр 35
-- В таблице Product найти модели, которые состоят только из цифр или только из латинских букв (A-Z, без учета регистра).
-- Вывод: номер модели, тип модели.
SELECT model, type FROM product WHERE model REGEXP '[a-zA-Z]' or model REGEXP '[0-9]'

-- упр 36
-- Перечислите названия головных кораблей, имеющихся в базе данных (учесть корабли в Outcomes).
select o.ship from outcomes o join classes c on o.ship = c.class
union
select s.name from ships s join classes c on s.name = c.class

-- упр 37
-- Найдите классы, в которые входит только один корабль из базы данных (учесть также корабли в Outcomes).
with tmp(name, class) AS (
select name, class from ships 
union
select o.ship, c.class from outcomes o join classes c on o.ship = c.class
)
select class from tmp group by class having COUNT(*) = 1

-- упр 38
-- Найдите страны, имевшие когда-либо классы обычных боевых кораблей ('bb') и имевшие когда-либо классы крейсеров ('bc').
select country from classes where type = 'bb' 
UNION
select country from classes where type = 'bc'

-- упр 39
-- Найдите корабли, `сохранившиеся для будущих сражений`; т.е. выведенные из строя в одной битве (damaged), 
-- они участвовали в другой, произошедшей позже
with tmp as (
select * from outcomes o join battles b on o.battle = b.name 
)
select distinct ship from tmp AS tmp1 where result = 'damaged' 
and exists(select * from tmp  AS tmp2 where tmp2.ship = tmp1.ship and tmp2.date > tmp1.date)

-- решение с форума
SELECT ship FROM 
Outcomes INNER JOIN Battles ON Outcomes.battle=Battles.name
GROUP BY ship 
HAVING MIN(CASE WHEN result='damaged' THEN date END)<MAX(date)

-- упр 40
-- Найти производителей, которые выпускают более одной модели, при этом все выпускаемые производителем модели являются продуктами одного типа.
-- Вывести: maker, type
-- ## что узнал? Оказывается можно делать так COUNT(DISTINCT model) Смотри решение с форума
with tmp as (
select  maker, type from product group by maker, type
)
select maker, type from tmp AS tmp1 
where  
(select count(*) from tmp AS tmp2 where tmp2.maker = tmp1.maker) = 1
and 
(select count(model) from product AS p where p.maker = tmp1.maker) > 1

-- решение с форума
SELECT maker, MAX(type)
  FROM Product
  GROUP BY maker
  HAVING COUNT(DISTINCT model) >   1
  AND
  COUNT(DISTINCT type) = 1

-- упр 41
-- Для каждого производителя, у которого присутствуют модели хотя бы в одной из таблиц PC, Laptop или Printer,
-- определить максимальную цену на его продукцию.
-- Вывод: имя производителя, если среди цен на продукцию данного производителя присутствует NULL, то выводить для этого производителя NULL,
-- иначе максимальную цену.

-- узнал, что агрегатные значения, count(value) работают с NULL значениями одним способом - игнорируют их,
-- а count(*) учитывает NULL при подсчете
with tmp as (
select maker, price from product p join laptop l on p.model = l.model
union all
select maker, price from product p join pc on p.model = pc.model
union all
select maker, price from product p join printer pr on p.model = pr.model
)
select maker,  
CASE
WHEN count(*) = count(price) THEN max(price)
WHEN count(*) != count(price) THEN NULL
END price
from tmp group by maker order by maker

-- упр 42
-- Найдите названия кораблей, потопленных в сражениях, и название сражения, в котором они были потоплены.
select ship, battle from outcomes where result = 'sunk'

-- упр 43 НЕ РЕШЕНО
-- Укажите сражения, которые произошли в годы, не совпадающие ни с одним из годов спуска кораблей на воду.
select name from battles where YEAR(date) not in (select launched from ships)

-- упр 44 
-- Найдите названия всех кораблей в базе данных, начинающихся с буквы R.
select name from ships where name like 'R%'
UNION
select ship from outcomes where ship like 'R%'

-- упр 45
-- Найдите названия всех кораблей в базе данных, состоящие из трех и более слов (например, King George V).
-- Считать, что слова в названиях разделяются единичными пробелами, и нет концевых пробелов.
select name from ships where name REGEXP '^.* .* .*$'
UNION
select ship from outcomes where ship REGEXP '^.* .* .*$'

select name from ships where name LIKE '% % %'
UNION
select ship from outcomes where ship LIKE '% % %'

-- упр 46
-- Для каждого корабля, участвовавшего в сражении при Гвадалканале (Guadalcanal), вывести название, водоизмещение и число орудий.
-- узнал:
-- 1) несколько join в одном запросе соединяются по указанным полям, а не по порядку следования
-- 2) можно делать так LEFT JOIN Classes B ON A.ship = B.class OR C.class = B.class
-- 3) и что в целом эта конструкция выстраивается в одну результирующую таблицу
SELECT
  ship, displacement, numGuns
  FROM Outcomes A
    LEFT JOIN Ships C ON A.ship = C.name
    LEFT JOIN Classes B ON A.ship = B.class OR C.class = B.class
  WHERE battle = 'Guadalcanal'
  
-- упр 47
-- Определить страны, которые потеряли в сражениях все свои корабли
-- узнал, что можно таки создавать несколько временных таблиц Это надо делать через запятую:
-- with tmp1 as (), tmp2 as ()

-- это мое НЕ рабочее решение (не понимаю почему)
with tmp as(
select  country, 
		a.class, 
        result,
        CASE
			WHEN name is NULL THEN a.class
			ELSE name
		END tmpname,
		CASE
			WHEN (select count(*) from outcomes where (ship = name or ship = a.class) and result = 'sunk') > 0 THEN 1
			ELSE 0
		END flag
from classes a
left join ships b on a.class = b.class
left join outcomes c on a.class = c.ship or b.name = c.ship
)
select distinct country from tmp where flag = 1 and country not in (select country from tmp where flag = 0)

-- это рабочее решение с инета         
with tmp as(
select country, name from classes c join ships s on c.class = s.class
union
select country, ship from classes c join outcomes o on c.class = o.ship
),
tmp1 as(
select country, count(*) total from tmp 
left join outcomes o on o.ship = tmp.name
where result = 'sunk' 
group by country
),
tmp2 as (
select country, count(*) total from tmp group by country
)
select tmp1.country from tmp1 join tmp2 on tmp1.country = tmp2.country where tmp1.total = tmp2.total
        
-- упр 48 
-- Найдите классы кораблей, в которых хотя бы один корабль был потоплен в сражении.
select 
 -- o.ship, c.class, o.result, c.country, c.numguns
  distinct c.class
 from outcomes o
	left join ships s on o.ship = s.name
    left join classes c on o.ship = c.class or s.class = c.class
where c.class is not null and result = 'sunk'  

-- упр 49
-- Найдите названия кораблей с орудиями калибра 16 дюймов (учесть корабли из таблицы Outcomes).
select s.name from classes c join ships s on c.class = s.class where bore = 16
union 
select o.ship from outcomes o join classes c on o.ship = c.class where bore = 16

-- упр 50
-- Найдите сражения, в которых участвовали корабли класса Kongo из таблицы Ships
select distinct o.battle from ships s join outcomes o on o.ship = s.name where class = 'Kongo'

-- упр 51
-- Найдите названия кораблей, имеющих наибольшее число орудий среди всех имеющихся кораблей такого же водоизмещения (учесть корабли из таблицы Outcomes).
with tmp as (
SELECT 
		s.name, c.numguns, c.displacement
        FROM Ships s
			LEFT JOIN Classes c on s.class = c.class
UNION
SELECT 
		o.ship, c.numguns, c.displacement
		FROM Outcomes o
			LEFT JOIN Classes c on o.ship = c.class
),
tmp1 as (select displacement, max(numguns) numguns from tmp group by displacement)
select tmp.name from tmp join tmp1 on tmp.displacement = tmp1.displacement and tmp.numguns = tmp1.numguns order by name

-- упр 52
-- Определить названия всех кораблей из таблицы Ships, которые могут быть линейным японским кораблем,
-- имеющим число главных орудий не менее девяти, калибр орудий менее 19 дюймов и водоизмещение не более 65 тыс.тонн
SELECT 
		name
        FROM Ships s
			LEFT JOIN Classes c on s.class = c.class
            where 
            country = 'Japan' 
            and type = 'bb' 
            and (numguns>=9 or numguns is null) 
            and (bore<19 or bore is null) 
            and (displacement<=65000 or displacement is null)
            
-- упр 53
-- Определите среднее число орудий для классов линейных кораблей. Получить результат с точностью до 2-х десятичных знаков.
-- мое решение - не проходит при проверке, у меня отображается верно
select ROUND(avg(numGuns*1.0), 2) from classes group by type having type = 'bb'	

 -- решение с форума у меня не проходит, проходит на сайте при проверке
select cast(avg(numguns*1.0) as numeric(6,2)) from classes group by type having type = 'bb'	

-- упр 54
-- С точностью до 2-х десятичных знаков определите среднее число орудий всех линейных кораблей (учесть корабли из таблицы Outcomes).
with tmp as (
select s.name, c.numGuns, c.type from ships s join classes c on c.class = s.class 
union
select ship, c.numGuns, c.type from outcomes o join classes c on o.ship = c.class
)
-- работает у меня
select ROUND(avg(numGuns*1.0), 2) from tmp group by type having type = 'bb'	
-- работает на проверке
select cast(avg(numguns*1.0) as numeric(6,2)) from tmp group by type having type = 'bb'

-- упр 55
-- Для каждого класса определите год, когда был спущен на воду первый корабль этого класса. Если год спуска на воду головного корабля 
-- неизвестен, определите минимальный год спуска на воду кораблей этого класса. Вывести: класс, год
select c.class, min(s.launched) from classes c left join ships s on c.class = s.class group by c.class

-- упр 56
-- Для каждого класса определите число кораблей этого класса, потопленных в сражениях. Вывести: класс и число потопленных кораблей.
with tmp as (
select c.class, s.name from classes c left join ships s on c.class = s.class
union
select c.class, o.ship from classes c join outcomes o on c.class = o.ship
), 
tmp2 as (
select tmp.class, tmp.name, o.result from tmp left join outcomes o on tmp.name = o.ship
)
select class, SUM(CASE WHEN result='sunk' THEN 1 ELSE 0 END) as sunks  from tmp2 group by class

-- упр 57
-- Для классов, имеющих потери в виде потопленных кораблей и не менее 3 кораблей в базе данных, вывести имя класса и число потопленных кораблей.
with tmp as (
		select c.class, s.name from classes c join ships s on c.class = s.class
		UNION
		select c.class, o.ship from outcomes o join classes c on o.ship = c.class
),
tmp1 as (
select * from tmp left join outcomes o on tmp.name = o. ship AND result = 'sunk'
),
tmp2 as (
select class, SUM(CASE WHEN result='sunk' THEN 1 ELSE 0 END) as countSunk from tmp1 group by class having count(name) > 2 
)
select * from tmp2 where countSunk > 0

-- упр 58
-- Для каждого типа продукции и каждого производителя из таблицы Product c точностью до двух десятичных знаков найти процентное отношение числа 
-- моделей данного типа данного производителя к общему числу моделей этого производителя.
-- Вывод: maker, type, процентное отношение числа моделей данного типа к общему числу моделей производителя

-- узнал, что есть EXCEPT - смотри решение с форума

-- работает у меня
with tmp as (
select a.maker, a.type, ROUND(p*100/g, 2) as nozero from (select maker, type, count(*) as p from product  group by maker, type order by maker) a
join (select maker, count(*) as g from product group by maker) as b on a.maker = b.maker
),
tmp1 as (
select a.maker, b.type, cast(0 as DEC(6,2)) as nozero from (select distinct maker from product) a, (select distinct type from product) b order by maker
)
select * from tmp 
UNION
select tmp1.maker, tmp1.type, tmp1.nozero from tmp
right outer join tmp1 on tmp.maker = tmp1.maker and tmp.type = tmp1.type where tmp.nozero is null

--  работает на проверке
with tmp as (
select a.maker, a.type, cast( ((cast(p AS DEC(6,4))) * 100 /g) as numeric(6,2)) as nozero from (select maker, type, count(*) as p from product  group by maker, type) a
join (select maker, count(*) as g from product group by maker) as b on a.maker = b.maker
),
tmp1 as (
select a.maker, b.type, cast(0 as DEC(6,2)) as nozero from (select distinct maker from product) a, (select distinct type from product) b
)
select * from tmp 
UNION
select tmp1.maker, tmp1.type, tmp1.nozero from tmp
right outer join tmp1 on tmp.maker = tmp1.maker and tmp.type = tmp1.type where tmp.nozero is null

-- красивое решение с форума с использованием EXCEPT
SELECT p1.maker, type, CAST(CAST(COUNT(model) AS real)*100/(
  SELECT COUNT(model)
  FROM product p2
  WHERE p2.maker = p1.maker
  GROUP BY maker) AS decimal(6,2)) AS perc
FROM product p1
GROUP BY maker, type
UNION
SELECT p1.maker, p2.type, .00
FROM product p1
CROSS JOIN product p2
EXCEPT
SELECT maker, type, .00
FROM product

-- упр 59
-- Посчитать остаток денежных средств на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день. Вывод: пункт, остаток.
with inco as (
select point, sum(i.inc) inc from income_o i group by point
),
outco as (
select point, sum(o.out) outc from outcome_o o group by point
),
res as (
select inco.point,  case when inc is NULL THEN 0 ELSE inc END inc, case when outc is NULL THEN 0 ELSE outc END outc from inco left join outco on inco.point = outco.point
union
select outco.point, case when inc is NULL THEN 0 ELSE inc END inc, case when outc is NULL THEN 0 ELSE outc END outc from inco right join outco on inco.point = outco.point
)
select point, (inc - outc) result from res

-- красивое решение с форума
with c as
(
	select point, date, inc from Income_o
	union
	select point, date, (-1)*o.out from Outcome_o o
)
select point, sum(inc)				
from c
group by point

-- упр 60
-- Посчитать остаток денежных средств на начало дня 15/04/01 на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день. Вывод: пункт, остаток.
-- Замечание. Не учитывать пункты, информации о которых нет до указанной даты.
with tmp as (
select i.point, i.inc, i.date from income_o i where i.date < '2001-04-15'
union
select o.point, -1*o.out, o.date from outcome_o o where o.date < '2001-04-15'
)
select point, sum(inc) from tmp group by point

-- упр 61
-- Посчитать остаток денежных средств на всех пунктах приема для базы данных с отчетностью не чаще одного раза в день.
with tmp as (
select i.point, i.inc, i.date from income_o i 
union
select o.point, -1*o.out, o.date from outcome_o o 
)
select sum(inc) from tmp

-- упр 62
-- Посчитать остаток денежных средств на всех пунктах приема на начало дня 15/04/01 для базы данных с отчетностью не чаще одного раза в день.
with tmp as (
select i.point, i.inc, i.date from income_o i where i.date < '2001-04-15'
union
select o.point, -1*o.out, o.date from outcome_o o where o.date < '2001-04-15'
)
select sum(inc) from tmp

-- упр 63
-- Определить имена разных пассажиров, когда-либо летевших на одном и том же месте более одного раза.
with tmp as 
(select distinct id_psg from pass_in_trip pt group by id_psg, place having count(*) > 1)
select pa.name from passenger pa join tmp on tmp.id_psg = pa.id_psg

-- упр 64
-- Используя таблицы Income и Outcome, для каждого пункта приема определить дни, когда был приход, но не было расхода и наоборот.
-- Вывод: пункт, дата, тип операции (inc/out), денежная сумма за день.

-- решение на проверочной таблице у меня проходит На сайте не проходит :
-- Column 'income.inc' is invalid in the select list because it is not contained in either an aggregate function or the GROUP BY clause
with inc as (
select point, sum(inc) money_sum, date, "inc" operation from income group by point, date
),
outc as (
select point, sum(o.out) money_sum, date, "out" operation from outcome o group by point, date
)
select inc.point, inc.date, inc.operation, inc.money_sum from inc left outer join outc on inc.date = outc.date and inc.point = outc.point
where outc.date is NULL
union
select outc.point, outc.date, outc.operation, outc.money_sum from outc left outer join inc on inc.date = outc.date and inc.point = outc.point
where inc.date is NULL

-- упр 66
-- у меня выполняется, при проверке ошибка синтаксиса: Incorrect syntax near the keyword 'with'

-- Для всех дней в интервале с 01/04/2003 по 07/04/2003 определить число рейсов из Rostov с пассажирами на борту.
-- Вывод: дата, количество рейсов.

-- узнал про рекурсивные запросы с WITH 
-- https://dev.mysql.com/doc/refman/8.4/en/with.html

with trip as (
with recursive Date_Ranges AS (
    select '2003-04-01 00:00:00' as Date, 0 as qty
   union all
   select Date + interval 1 day, 0
   from Date_Ranges
   where Date < '2003-04-08'
)
select date, count(distinct pt.trip_no) qty from trip tr join pass_in_trip pt on tr.trip_no = pt.trip_no and tr.town_from = 'Rostov' 
group by pt.trip_no, pt.date 
having pt.date between '2003-04-01' and '2003-04-07'
UNION
select * from Date_Ranges
)
select * from trip order by date

-- упр 71 Найти тех производителей ПК, все модели ПК которых имеются в таблице PC
select distinct maker from product pro where pro.type = 'pc' and maker not in 
(select distinct maker from product pro left outer join pc on pro.model = pc.model 
where 
pro.type = 'pc'
and pc.code is null)


-- все что ниже - вспомогательные запросы для частого использования/изучения

SELECT COALESCE(NULL, NULL, 'third_value', 'fourth_value')

-- aero
select * from company
select * from pass_in_trip
select * from passenger
select * from trip

-- firm
select * from income
select * from income_o
select * from outcome
select * from outcome_o

-- web
select a.maker, b.type, cast(0 as DEC(6,2)) as zero from (select distinct maker from product) a, (select distinct type from product) b order by maker
select maker, type, count(*) as p from product  group by maker, type
select maker, type, 

-- -------------
insert into outcomes(ship, battle, result) values ('California', 'tmpBattle', 'sunk')        
insert into battles(name, date) values ('tmpBattle', '1942-11-15')        

select * from outcomes order by ship
select * from battles
select * from classes
select * from ships
select YEAR(date) from battles
select DATEPART(year, date) from battles
select * from ships order by name
select * from printer
select * from pc
select * from product order by maker
use web;
SELECT VERSION();






