# SQL-Project

# WW Capital Ship Database

World War 2 capital ships database contains the following relations:
Classes(class, type, country, numGuns, bore, displacement)
Ships(name, class, launched)
Battles(name, date)
Outcomes(ship, battle, result)

The relation Classes records the name of the class, the type(‘bb’ for battleship or ‘bc’ for battlecruiser), the country that build the ship, the number of main guns, the bore (diameter of the gun), and the displacement(weight,
in tons). Relation Ships records the name of the ship, the name of its class, and the year in which the ship was
launched. Relation Battles gives the name and date of battles involving these ships, and relation Outcomes gives
the result (sunk, damaged, or ok) for each ship in each battle.

## Table: Battles

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/cd4baf0f-cd32-45f6-bfe5-27e3aa7eb526)

## Table: Ships

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/511f1b2b-3999-45e8-b716-2ef816421a22)


## Table: Outcomes

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/e722d78b-83ba-4275-b0e2-0257f462d731)


## Table: Classes

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/0115dc06-4bf9-47a8-997c-3650a451fb25)


## Find the classes of ships, at least one of which was sunk in a battle.

## Method-1: Using EXISTS

SELECT class
FROM Ships b1
WHERE EXISTS (SELECT * 
       FROM Outcomes b2 
       WHERE b1.name = b2.ship AND b2.result = ‘sunk’);

 ![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/268217c0-e263-40a5-b363-5238251562c7)

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/30465b18-39ba-4223-a943-856a652db232)

 

## Method-2: Using IN

SELECT class
FROM Ships b1
WHERE name IN (SELECT ship 
       FROM Outcomes b2
       WHERE b2.result = ‘sunk’);

 
![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/87a4e965-63a0-4e78-921d-a749dbb2a4e2)

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/209a5377-465f-4781-b865-350a1af9047f)



## Find the names of the ships whose number of guns was the largest for those ships of the same bore.

All the ships belonging to the same class had similar designs including the number of guns.

## Method-1: 

I am using the case where bore = 15. There are 3 classes which come under this category – Renown (6), Revenge (8) and Bismarck (8). Renown does not have the largest number of guns. Bismarck does, but is absent in the relation, Ships. So, only relevant class is Revenge, which has 5 ship names associated with it.

WITH f15 AS (
		  SELECT MAX(c15.numGuns_15) AS m15
		  FROM (SELECT class, GREATEST(numGuns) AS numGuns_15
			    FROM Classes
			    WHERE bore = 15) c15),
           S15 AS (
		  SELECT class AS sclass, numGuns AS snumGuns
		  FROM Classes
		  WHERE bore = 15)
SELECT Ships.name
FROM Ships, f15, s15
WHERE s15.snumGuns = f15.m15 AND s15.sclass = Ships.class;

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/d1e675a3-0008-4926-a07b-e853c3a6533d)

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/ca53434e-cf69-4b36-ae16-671f36399ff0)

## Method-2:

WITH temp AS (
SELECT bore, max(numGuns) OVER (PARTITION BY bore) maxG,                
     ROW_NUMBER() OVER (PARTITION BY bore) row_num 
     FROM Classes)
SELECT Ships.name, Ships.class, Classes.bore, Classes.numGuns
FROM Classes, Ships, temp
WHERE Classes.bore = temp.bore AND Classes.numGuns = temp.maxG AND Classes.class = Ships.class AND temp.row_num = 1;

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/755623a1-e795-4c81-9706-c176d13afce4)

![image](https://github.com/maddies-codespace/SQL-Project/assets/141537679/a0bbcd84-a57d-41ba-a4d6-9ae913b9df5d)






 

