# Esercitazione 1 SQL
## Schema delle query e ordine di esecuzione
5 SELECT Attributes + Expressions <br>
1 FROM Tables + Joins <br>
2 [WHERE Conditions] <br>
3 [GROUP BY Attributes] <br>
4 [HAVING Aggregated conditions] <br>
6 [ORDER BY Ordering attribute] <br>

## Es 1 - Exams.db
*Write a SQL query that returns the distinct surnames that belong to both students and lecturers.* 

SELECT DISTINCT S.surname <br>
FROM STUDENT AS S, LECTURER AS L <br>
WHERE S.Surname = L.Surname

## Es 2 - Exams.db
*Write a query that returns the surnames of those lecturers that have taught in courses for which al leaste 10 students passed the exam.* 

SELECT DISTINCT L.Surname <br>
FROM EDITION AS D, EXAM AS E, LECTURER AS L <br>
WHERE D.Course=E.Course AND L.Id=D.Lecturer AND D.Year=E.Year <br>
GROUP BY L.Id, L.Surname, D.Course, D.Year<br>
HAVING COUNT(*)>10 <br>

## Es 3 - train.db
*Write a query that returns the minimum and maximum length of the routes between Boston and Chicago without interchanges.* 

SELECT MIN(MILE) AS Min, MAX(MILE) AS Max<br>
FROM TRAIN <br>
WHERE DEPARTURE='Boston' AND ARRIVAL='Chicago'<br>

## Es 4 - region.db
*Write a query that returns the regions having more inhabitants than residents.* 

CREATE VIEW C AS <br>
SELECT Region, COUNT(*) AS Citizen <br>
FROM RESIDENCE<br>
GROUP BY REGION;<br>

SELECT RG.Name<br>
FROM REGION AS RG, C<br>
WHERE RG.Name=C.Region AND RG.Population>C.Citizen<br>

## Es 5 - Film.db
*Write a query thet returns the titles of those films produced between 1990 and 2000, having an average rating greater or equal to 7.*

SELECT DISTINCT F.Title<br>
FROM FILM AS F, RATING AS R<br>
WHERE F.FilmID=R.FilmID AND F.Year>=1990 AND F.Year<=2000<br>
GROUP BY F.Title, F.FilmID<br>
HAVING AVG(R.Rating)>=7<br>

## Es 6 - Film.db
*Write a query thet returns the minimum age of users who rated "Blade Runner" greater than 8.*

SELECT MIN(U.Age)<br>
FROM USER AS U, RATING AS R, FILM AS F<br>
WHERE F.Title='Blade Runner' AND F.FilmID=R.FilmID AND R.UserID=U.UserID AND R.Rating>8<br>

oppure

SELECT MIN(U.Age)<br>
FROM USER AS U NATURAL JOIN RATING AS R NATURAL JOIN FILM AS F<br>
WHERE F.Title='Blade Runner' AND R.Rating>8<br>

## Es 7 - Song.db
*Write a query thet returns the name of those playlists having at least one song from an album published before 2001 by an UMG artist .*

CREATE VIEW TRACKS AS<br>
SELECT S.Title, S.ID<br>
FROM ARTIST AS AR JOIN ALBUM AS AL ON AR.ID=AL.Artist JOIN SONG AS S ON S.Album=AL.ID<br>
WHERE AL.Year<2001 AND AR.Labels='UMG';<br>

SELECT DISTINCT P.Name<br>
FROM TRACKS AS T, PLAYLIST AS P <br>
WHERE P.Song=T.ID<br>

## Es 8 - Film2.db
*Write a query thet returns the title of Fellini's films produced after 1960.*

SELECT F.Title<br>
FROM FILM AS F<br>
WHERE F.Year>1960 AND F.Director='Federico Fellini'<br>

## Es 9 - Film2.db
*Write a query that returns the title of Japanese science fiction films produced after 1990 or French science fiction films*

SELECT Title<br>
FROM FILM<br>
WHERE Year>1990 AND Country='Japan' AND Genre='Sci-fi'<br>
UNION<br>
SELECT Title<br>
FROM FILM<br>
WHERE Country='France' AND Genre='Sci-fi'<br>

## Es 10 - Film2.db
*Write a query that returns the title and genre of films screened in London on last Christmas Day.*

SELECT F.Title, F.Genre<br>
FROM SCREENING AS S JOIN ROOM AS R ON S.Room=R.Code JOIN FILM AS F ON F.Code=S.Film<br>
WHERE R.City='London' AND S.Date=2023-12-25<br>

## Es 11 - Film2.db
*Write a query that returns the number of screening rooms in London with more than 60 seats.*

SELECT COUNT(*)<br>
FROM ROOM AS R <br>
WHERE R.Seats>=60 AND R.City='London'<br>

## Es 12 - Film2.db
*Write a query that returns for each pair of director and actor their names, and the number of films where they worked together.*

SELECT F.Director, A.Name, COUNT(*) AS Total<br>
FROM FILM AS F, RECITAL AS R, ACTOR AS A<br>
WHERE F.Code=R.Film AND A.Id=R.Actor<br>
GROUP BY F.Director, A.Id, A.Name<br>

## Es 13 - Film2.db
*Write a query that returns the director and the title of films in which less than 6 actors act.*

SELECT DISTINCT F.Director, F.Title<br>
FROM FILM AS F, RECITAL AS R<br>
WHERE F.Code=R.Film<br>
GROUP BY F.Director, F.Title, F.Code<br>
HAVING COUNT(R.Actor)<6<br>

### Come usare GROUP BY e HAVING

Filtrare i record originari (WHERE): Isola i dati grezzi prima che vengano uniti e raggruppati. Inserisci qui le condizioni sugli attributi semplici delle singole tuple (es. Anno >= 1990, Genere = 'Horror'). Il WHERE elabora riga per riga e non accetta mai funzioni di aggregazione.

Definire i gruppi (GROUP BY): Identifica l'entità logica su cui devi calcolare i totali, le medie o i conteggi (es. "per ogni film", "per ogni studente"). Inserisci l'identificatore univoco di quell'entità (es. F.Code o Matricola) e aggiungi tutti gli attributi descrittivi che intendi visualizzare nel risultato finale (es. F.Title, F.Director).

Proiettare i risultati (SELECT): Inserisci gli attributi dichiarati nel GROUP BY e affiancali alle funzioni di calcolo richieste (COUNT, SUM, AVG, MAX, MIN). Applica la regola sintattica assoluta: **qualsiasi colonna inserita nella SELECT che non sia all'interno di una funzione aggregata deve obbligatoriamente comparire nel GROUP BY.**

Filtrare i risultati aggregati (HAVING): Applica le condizioni matematiche ai gruppi interi formati al punto 2. Utilizza l'HAVING esclusivamente per valutare il risultato delle funzioni di aggregazione (es. HAVING COUNT(*) < 6 o HAVING AVG(Rating) >= 7). Se una condizione non richiede un calcolo aggregato, deve stare nel WHERE.

## Es 14 - Film2.db
*Write a query that returns the name and the whole takings of those rooms in Rome that in January 2005 have gained more than 20000 $.*

SELECT R.Name, SUM(S.Profits) AS Taking<br>
FROM SCREENING AS S JOIN ROOM AS R ON S.Room=R.Code<br>
WHERE S.Date>='2005-01-01' AND S.Date<='2005-01-31' AND R.City='Rome'<br>
GROUP BY R.Code, R.Name<br>
HAVING SUM(S.Profits)>20000<br>