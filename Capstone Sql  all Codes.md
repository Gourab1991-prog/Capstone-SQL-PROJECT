# Capstone-SQL-PROJECT
SQL REAL TIME PROJECT
1.	Represent the “book_date” column in “yyyy-mmm-dd” format using Bookings table
Expected output: book_ref, book_date (in “yyyy-mmm-dd” format) , total amount 
Answer:
SELECT  book_ref,
TO_CHAR(book_date,'yyyy-mmm-dd') AS book_date,
total_amount
FROM BOOKINGS
4.	Write a query to find the seat number which is least allocated among all the seats?

Answer: 
SELECT Seat_no FROM (SELECT Seat_no,COUNT(*)AS seat_count
FROM BOARDING_PASSES GROUP BY Seat_no ORDER BY seat_count 
LIMIT 1) AS Seat_no
5.	In the database, identify the month wise highest paying passenger name and passenger id.
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total amount.


Answer:  WITH MONTHLY_MAXIMUM_AMOUNT AS (SELECT 
TO_CHAR(book_date, 'mmm-yy') AS month_name,
t.passenger_id,
t.passenger_name,
b.total_amount,
ROW_NUMBER()OVER(PARTITION BY TO_CHAR(book_date, 'mmm-yy') ORDER BY b.total_amount DESC) AS number 
FROM BOOKINGS B 
JOIN TICKETS T 
ON T.book_ref=B.book_ref)
SELECT  month_name,
passenger_id,
passenger_name,
total_amount
FROM MONTHLY_MAXIMUM_AMOUNT 
WHERE number=1
ORDER BY month_name


6.	In the database, identify the month wise least paying passenger name and passenger id?
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total amount

Answer:   WITH MONTHLY_MAXIMUM_AMOUNT AS (SELECT 
TO_CHAR(book_date, 'mmm-yy') AS month_name,
t.passenger_id,
t.passenger_name,
b.total_amount,
ROW_NUMBER()OVER(PARTITION BY TO_CHAR(book_date, 'mmm-yy') ORDER BY b.total_amount ASC) AS number 
FROM BOOKINGS B 
JOIN TICKETS T 
ON T.book_ref=B.book_ref)
SELECT  month_name,
passenger_id,
passenger_name,
total_amount
FROM MONTHLY_MAXIMUM_AMOUNT 
WHERE number=1
ORDER BY month_name


7.	Identify the travel details of non stopjourneys  or return journeys (having more than 1 flight).
Expected Output: Passenger_id, passenger_name, ticket_number and flight count.

Answer: SELECT t.passenger_id,
t.passenger_name,
t.ticket_no,
COUNT(tf.flight_id) AS flight_count 
FROM TICKETS T 
JOIN TICKET_FLIGHTS TF 
ON T.ticket_no=TF.ticket_no 
GROUP BY t.passenger_id,t.passenger_name,t.ticket_no 
HAVING COUNT(tf.flight_id)=1 OR COUNT(tf.flight_id)>1

8.	How many tickets are there without boarding passes?
Expected Output: just one number is required.

Answer: 251


9.	Identify details of the longest flight (using flights table)?
Expected Output: Flight number, departure airport, arrival airport, aircraft code and durations.

Answer:
 SELECT 
flight_id,
flight_no,
departure_airport,
arrival_airport,
(actual_departure-actual_arrival)/60 AS DURATION 
FROM FLIGHTS
ORDER BY DURATION DESC
LIMIT 1




10.	Identify details of all the morning flights (morning means between 6AM to 11 AM, using flights table)?
Expected output: flight_id, flight_number, scheduled_departure, scheduled_arrival and timings.

Answer:
 SELECT 
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
CAST(scheduled_departure AS time) AS TIMING
FROM FLIGHTS
WHERE CAST(scheduled_departure AS time) BETWEEN '06:00:00' AND '11:00:00'




11.	Identify the earliest morning flight available from every airport.
Expected output:flight_id, flight_number, scheduled_departure, scheduled_arrival, departure airport and timings.
Answer:  
WITH EARLY_MORNING_FLIGHTS AS (SELECT 
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
departure_airport,
CAST(scheduled_departure AS time) AS timing,
ROW_NUMBER ()OVER (PARTITION BY departure_airport ORDER BY scheduled_departure) AS row_num
FROM FLIGHTS
WHERE CAST(scheduled_departure AS time) BETWEEN '06:00:00' AND '11:00:00')
SELECT 
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
departure_airport,
timing
FROM EARLY_MORNING_FLIGHTS
WHERE row_num=1

12.	Questions:Find list of airport codes in Europe/Moscow timezone
Expected Output:  Airport_code.

Answer: 
SELECT 
airport_code
FROM AIRPORTS 
WHERE timezone='Europe/Moscow'

13.	Write a query to get the count of seats in various fare condition for every aircraft code?
Expected Outputs: Aircraft_code, fare_conditions ,seat count

Answer:  SELECT 
aircraft_code,
fare_conditions,
COUNT (*) AS seat_count
FROM SEATS 
GROUP BY aircraft_code,fare_conditions
ORDER BY aircraft_code,fare_conditions

14.	How many aircrafts codes have at least one Business class seats?
Expected Output : Count of aircraft codes

Answer: SELECT 
COUNT (DISTINCT aircraft_code) AS count_of_aircraft 
FROM SEATS 
WHERE fare_conditions='Business'
15.	Find out the name of the airport having maximum number of departure flight
Expected Output :Airport_name

Answer: SELECT 
airport_name
FROM AIRPORTS 
WHERE airport_code=(SELECT
departure_airport 
FROM FLIGHTS 
GROUP BY departure_airport 
ORDER BY COUNT (*) DESC LIMIT 1)

16.	Find out the name of the airport having least number of scheduled departure flights
Expected Output :Airport_name

Answer: 
SELECT 
airport_name
FROM AIRPORTS 
WHERE airport_code=(SELECT
departure_airport 
FROM FLIGHTS 
GROUP BY departure_airport 
ORDER BY COUNT (*) ASC LIMIT 1)

17.	How many flights from ‘DME’ airport don’t have actual departure?
 Expected Output : Flight Count 

Answer: SELECT 
COUNT(*) AS FLIGHT_COUNT
FROM FLIGHTS 
WHERE departure_airport ='DME' AND actual_departure IS NULL

18.	Identify flight ids having range between 3000 to 6000
Expected Output :Flight_Number , aircraft_code, ranges 

Answer:  SELECT 
f.flight_no,
a.aircraft_code,
a.range
FROM FLIGHTS F
JOIN AIRCRAFTS A
ON A.aircraft_code=F.aircraft_code
WHERE a.range BETWEEN 3000 AND 6000
GROUP BY f.flight_no,a.aircraft_code,a.range
ORDER BY a.range


19.	Write a query to get the count of flights flying between URS and KUF?
Expected Output :Flight_count

Answer: SELECT 
COUNT (*) AS Flight_count
FROM FLIGHTS
WHERE departure_airport='URS' 
AND arrival_airport='KUF'



20.	Write a query to get the count of flights flying from either from NOZ or KRR?
 Expected Output : Flight count 

Answer: SELECT 
 COUNT (*) AS Flight_count 
 FROM FLIGHTS 
 WHERE departure_airport='NOZ'
 OR departure_airport='KRR'


21.	Write a query to get the count of flights flying from KZN,DME,NBC,NJC,GDX,SGC,VKO,ROV
Expected Output : Departure airport ,count of flights flying from these   airports.

Answer: SELECT 
 departure_airport AS departure_airport,
 COUNT (*) AS flight_count 
 FROM FLIGHTS 
 WHERE departure_airport IN ('KZN','DME','NBC','NJC','GDX','SGC','VKO','ROV')
 GROUP BY departure_airport
 ORDER BY flight_count


22.	Write a query to extract flight details having range between 3000 and 6000 and flying from DME
Expected Output :Flight_no,aircraft_code,range,departure_airport

Answer: SELECT f.flight_no,
 f.aircraft_code,
 a.range,
 f.departure_airport
 FROM FLIGHTS F 
 JOIN AIRCRAFTS A 
 ON F.aircraft_code=A.aircraft_code
 WHERE a.range BETWEEN 3000 AND 6000 AND departure_airport='DME'
 GROUP BY f.flight_no,f.aircraft_code,a.range,f.departure_airport
 ORDER BY a.range

23.	Find the list of flight ids which are using aircrafts from “Airbus” company and got cancelled or delayed
Expected Output :Flight_id,aircraft_model

Answer: SELECT f.flight_id,
a.model AS aircraft_model
FROM FLIGHTS F 
JOIN AIRCRAFTS A 
ON A.aircraft_code=F.aircraft_code 
WHERE a.model LIKE '%Airbus%' AND 
f.status='Cancelled' OR f.status='Delayed'


24.	Find the list of flight ids which are using aircrafts from “Boeing” company and got cancelled or delayed
Expected Output :Flight_id,aircraft_model

Answer: SELECT f.flight_id,
a.model AS aircraft_model
FROM FLIGHTS F 
JOIN AIRCRAFTS A 
ON A.aircraft_code=F.aircraft_code 
WHERE a.model LIKE '%Boeing%' AND 
f.status='Cancelled' OR f.status='Delayed'


25.	Which airport(name) has most cancelled flights (arriving)?
Expected Output :Airport_name
Answer: SELECT a.airport_name
FROM AIRPORTS A
JOIN FLIGHTS F  
ON A.airport_code=F.arrival_airport 
WHERE f.status='Cancelled'
GROUP BY a.airport_name
ORDER BY COUNT(*) DESC
LIMIT 1
Answer: {en": "Begishevo Airport", "ru": "}
26.	Identify flight ids which are using “Airbus aircrafts”
Expected Output :Flight_id,aircraft_model


Answer: 
SELECT f.flight_id,
a.model AS aircraft_model
FROM FLIGHTS F 
JOIN AIRCRAFTS A 
ON A.aircraft_code=F.aircraft_code 
WHERE a.model like '%Airbus%'


27.	Identify date-wise last flight id flying from every airport?
Expected Output: Flight_id,flight_number,schedule_departure,departure_airport

Answer: WITH LAST_FLIGHT AS (SELECT f.flight_id,
f.flight_no,
f.scheduled_departure,
f.departure_airport,
MAX(f.scheduled_departure)OVER (PARTITION BY f.departure_airport,
DATE(f.scheduled_departure)) AS max_scheduled_departure
FROM FLIGHTS AS F)
SELECT flight_id,
flight_no,scheduled_departure,
departure_airport 
FROM LAST_FLIGHT 
WHERE scheduled_departure=max_scheduled_departure
ORDER BY 2


28.	Identify list of customers who will get the refund due to cancellation of the flights and how much amount they will get?
Expected Output : Passenger_name,total_refund.

Answer: WITH REFUND_PASSENGER AS (SELECT t.passenger_name,
f.status,
SUM(tf.amount) as total_refund
FROM TICKETS T 
INNER JOIN TICKET_FLIGHTS TF 
ON T.ticket_no=TF.ticket_no
INNER JOIN FLIGHTS F 
ON TF.flight_id=F.flight_id
WHERE f.status='Cancelled'
GROUP BY t.passenger_name,f.status)
SELECT passenger_name,total_refund
FROM REFUND_PASSENGER


29.	Identify date wise first cancelled flight id flying for every airport?
Expected Output :Flight_id,flight_number,schedule_departure,departure_airport

Answer: SELECT flight_id,
flight_no,
scheduled_departure,
departure_airport
FROM(SELECT flight_id,
flight_no,
scheduled_departure,
departure_airport,
ROW_NUMBER () OVER (PARTITION BY departure_airport ORDER BY scheduled_departure ASC) AS R 
FROM FLIGHTS
WHERE status='Cancelled') AS C 
WHERE R=1
ORDER BY departure_airport,scheduled_departure

30.	Identify list of Airbus flight ids which got cancelled.
Expected Output :Flight_id

Answer: SELECT 
f.flight_id 
FROM FLIGHTS F 
JOIN AIRCRAFTS A 
ON F.aircraft_code=A.aircraft_code 
WHERE a.model LIKE'%Airbus%' AND f.status='Cancelled'

31.	Identify list of flight ids having highest range.
Expected Output : Flight_no, range


Answer: 
SELECT 
f.flight_no,
MAX(a.range) AS range
FROM FLIGHTS F 
JOIN AIRCRAFTS A 
ON F.aircraft_code=A.aircraft_code 
GROUP BY f.flight_no

