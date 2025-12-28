# SQL Advent Calendar Challenge 2025

## 📌 Projeye Genel Bakış
 
**Komut Dosyası:** [`SQL Advent Calendar Solutions`](script/SQL-Advent-Calendar-Solutions.sql)

Bu proje, Interview Master'ın Aralık ayı boyunca yayınlanan **SQL Advent Calendar** challange sorularının tamamına ait çözümlerimi içermektedir.  

**Açıklama:** Tüm sorgular **SQLite** üzerinde yazılmış olup, farklı zorluk seviyelerinde (Easy / Medium / Hard) verilen senaryolar üzerinden analitik SQL becerilerini sergilemektedir.  

---

## 🎄 Yarışma Hakkında

SQL Advent Calendar, [Interview Master](https://interviewmaster.ai/advent) tarafından düzenlenen ve 24 gün boyunca her gün bir SQL problemi sunan şenlikli bir kodlama etkinliğidir.  
Katılımcılar her gün yeni bir sorgulama problemiyle karşılaşır ve SQL becerilerini geliştirir.
 <P></P>
 
<img width="2819" height="1462" alt="Ekran görüntüsü 2025-12-25 000017" src="https://github.com/user-attachments/assets/683553bb-dbed-4144-9974-86401996494b" />

---

## 💡 Kullanılan SQL Teknikleri

Kullanılan SQL teknikleri aşağıda listelenmiştir:

- **DQL (Data Query Language)**
  - `SELECT`, `DISTINCT`, `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING`, `LIMIT`, `JOIN` (INNER JOIN, LEFT JOIN,CROSS JOIN)
  - Amaç: Veriyi filtrelemek, sıralamak, ilişkilendirmek ve analiz etmek.

- **Koşullu ve Analitik Yapılar**
  - `CASE WHEN`, `CTE (WITH)`, `SUBQUERY`,
  - `WINDOW FUNCTIONS`: `ROW_NUMBER()`, `LAG()`, `SUM() OVER()`
  - Amaç: Gruplama, sıralama, karşılaştırma ve zaman bazlı analizler yapmak.

- **Aggregate & Fonksiyonlar**
  - `COUNT()`, `COUNT(DISTINCT ...)`, `SUM()`, `AVG()`, `MIN()`, `MAX()`, `ROUND()`
  - String fonksiyonları: `LOWER()`, `UPPER()`, `SUBSTR()`
  - Tarih fonksiyonları: `DATE()`
  
---
---

## 📜 SQL Sorguları

Aşağıda her gün için **zorluk seviyesi**, ilgili SQL sorusu, tablo şeması ve çözüm sorgusu almaktadır.

## Day 1 – Reindeer Run 🟢 Easy

**Question:**
- Every year, the city of Whoville conducts a Reindeer Run to find the best reindeers for Santa's Sleigh. Write a query to return the name and rank of the top 7 reindeers in this race.

**Table Schema:**
- Table: reindeer_run_results
    - number: INTEGER
    - name: VARCHAR
    - rank: INTEGER
    - color: VARCHAR

```sql
SELECT name, rank
FROM reindeer_run_results
ORDER BY rank
LIMIT 7;
```
➡️ En iyi sıralamaya sahip ilk 7 kaydı getirir.

---

## Day 2 – Toys Delivered 🟡 Medium

**Question:**
- Santa wants to analyze which toys that were produced in his workshop have already been delivered to children. You are given two tables on toy production and toy delivery — can you return the toy_id of the toys that have been delivered?


**Table Schema:**
- Table: toy_production
  -   toy_id: INTEGER
  -   toy_name: VARCHAR
  -   production_date: DATE

- Table: toy_delivery
   -   toy_id: INTEGER
  -  child_name: VARCHAR
  -  delivery_date: DATE

```sql
SELECT d.toy_id, t.toy_name
FROM toy_delivery d
JOIN toy_production t ON d.toy_id = t.toy_id;
```
➡️ Teslim edilen oyuncakları üretim tablosu ile eşleştirir.

---

## Day 3 – The Grinch's Best Pranks Per Target 🔴 Hard 

**Question:**
- The Grinch has brainstormed a ton of pranks for Whoville, but he only wants to keep the top prank per target, with the highest evilness score. Return the most evil prank for each target. If two pranks have the same evilness, the more recently brainstormed wins.


**Table Schema:**
- Table: grinch_prank_ideas
  -   prank_id: INTEGER
  -   target_name: VARCHAR
  -   prank_description: VARCHAR
  -   evilness_score: INTEGER
  -   created_at: TIMESTAMP


```sql
SELECT prank_id, target_name, prank_description, evilness_score, created_at
FROM (
  SELECT *,
         ROW_NUMBER() OVER (
           PARTITION BY target_name
           ORDER BY evilness_score DESC, created_at DESC
         ) AS rn
  FROM grinch_prank_ideas
) t
WHERE rn = 1;
```
➡️ Her hedef için en yüksek skora sahip tek şaka seçilir.

---

## Day 4 – Energy-Efficient Holiday Decorations 🟢 Easy

**Question:**
- Kevin's trying to decorate the house without sending the electricity bill through the roof. Write a query to find the top 5 most energy-efficient decorations (i.e. lowest cost per hour to operate).


**Table Schema:**
- Table: hall_decorations
  -   decoration_id: INT
  -   decoration_name: VARCHAR
  -   energy_cost_per_hour: DECIMAL

```sql
SELECT decoration_name, energy_cost_per_hour
FROM hall_decorations
ORDER BY energy_cost_per_hour
LIMIT 5;
```

---

## Day 5 – Elf Vacation Status 🟡 Medium

**Question:**
- Some elves took time off after the holiday rush, but not everyone has returned to work. List all elves by name, showing their return date. If they have not returned from vacation, list their return date as "Still resting".


**Table Schema:**
- Table: elves
  -   elf_id: INT
  -   elf_name: VARCHAR

- Table: vacations
  -   elf_id: INT
  -   start_date: DATE
  -   return_date: DATE


```sql
SELECT e.elf_id, e.elf_name,
  CASE
    WHEN v.start_date IS NULL THEN ''
    WHEN v.return_date IS NULL THEN 'Still resting'
    ELSE v.return_date
  END AS return_status
FROM elves e
LEFT JOIN vacations v ON e.elf_id = v.elf_id;
```

---

## Day 6 – Ski Resort Snowfall Rankings 🔴 Hard

**Question:**
- Buddy is planning a winter getaway and wants to rank ski resorts by annual snowfall. Can you help him bucket these ski resorts into quartiles?


**Table Schema:**
- Table: resort_monthly_snowfall
  -   resort_id: INT
  -   resort_name: VARCHAR
  -   snow_month: INT
  -   snowfall_inches: DECIMAL

```sql
WITH yearly AS (
    SELECT 
        resort_id,
        resort_name,
        SUM(snowfall_inches) AS total_snow
    FROM resort_monthly_snowfall
    GROUP BY resort_id, resort_name
),
ordered AS (
    SELECT 
        resort_id,
        resort_name,
        total_snow,
        ROW_NUMBER() OVER (ORDER BY total_snow DESC) AS rn,
        COUNT(*) OVER () AS total_count
    FROM yearly
)
SELECT
    resort_id,
    resort_name,
    total_snow,
    CASE
        WHEN rn <= total_count * 0.25 THEN 1
        WHEN rn <= total_count * 0.50 THEN 2
        WHEN rn <= total_count * 0.75 THEN 3
        ELSE 4
    END AS quartile
FROM ordered
ORDER BY quartile, total_snow DESC;
```

➡️ Yıllık kar miktarına göre CTE ve `ROW_NUMBER()` ile çeyreklik hesaplama yapılır.

---

## Day 7 – Snowflake Types Count 🟢 Easy

**Question:**
- Frosty wants to know how many unique snowflake types were recorded on the December 24th, 2025 storm. Can you help him?


**Table Schema:**
- Table: snowfall_log
  -   flake_id: INT
  -   flake_type: VARCHAR
  -   fall_time: TIMESTAMP

```sql
SELECT COUNT(DISTINCT flake_type)
FROM snowfall_log
WHERE DATE(fall_time) = '2025-12-24';
```

---

## Day 8 –  Storage Room Inventory 🟡 Medium

**Question:**
- Mrs. Claus is organizing the holiday storage room and wants a single list of all decorations — both Christmas trees and light sets. Write a query that combines both tables and includes each item's name and category.


**Table Schema:**
- Table: storage_trees
  -   item_name: VARCHAR
  -   category: VARCHAR
  
- Table: storage_lights
  -   item_name: VARCHAR
  -   category: VARCHAR
    
```sql
SELECT item_name, category FROM storage_trees
UNION
SELECT item_name, category FROM storage_lights;
```

---

## Day 9 – Tinsel and Light Combinations 🔴 Hard

**Question:**
- The elves are testing new tinsel–light combinations to find the next big holiday trend. Write a query to generate every possible pairing of tinsel colors and light colors, include in your output a column that combines the two values separated with a dash ("-").


**Table Schema:**
- Table: tinsel_colors
  -  tinsel_id: INT
  -   color_name: VARCHAR

- Table: light_colors
  -   light_id: INT
  -   color_name: VARCHAR

```sql
SELECT  t.tinsel_id, l.light_id,
        t.color_name || '-' || l.color_name AS combo
        --MYSQL: CONCAT(t.color_name, '-', l.light_name) AS combo
FROM tinsel_colors t
CROSS JOIN light_colors l;
```

---

## Day 10 – Cookie Factory Oven Efficiency 🟢 Easy

**Question:**
- In the holiday cookie factory, workers are measuring how efficient each oven is. Can you find the average baking time per oven rounded to one decimal place?


**Table Schema:**
- Table: cookie_batches
  -   batch_id: INT
  -   oven_id: INT
  -   baking_time_minutes: DECIMAL

```sql
SELECT oven_id, ROUND(AVG(baking_time_minutes),1)
FROM cookie_batches
GROUP BY oven_id;
```

---

## Day 11 – Winter Market Sweater Search 🟡 Medium

**Question:**
- At the winter market, Cindy Lou is browsing the clothing inventory and wants to find all items with "sweater" in their name. But the challenge is the color and item columns have inconsistent capitalization. Can you write a query to return only the sweater names and their cleaned-up colors.


**Table Schema:**
- Table: winter_clothing
  -   item_id: INT
  -   item_name: VARCHAR
  -   color: VARCHAR

```sql
SELECT item_name,
       UPPER(SUBSTR(color,1,1)) || LOWER(SUBSTR(color,2)) AS cleaned_color
FROM winter_clothing
WHERE LOWER(item_name) LIKE '%sweater%';
```

---

## Day 12 – North Pole Network Most Active Users 🔴 Hard

**Question:**
- The North Pole Network wants to see who's the most active in the holiday chat each day. Write a query to count how many messages each user sent, then find the most active user(s) each day. If multiple users tie for first place, return all of them.


**Table Schema:**
- Table: npn_users
  -   user_id: INT
  -   user_name: VARCHAR
    
- Table: npn_messages
  -   message_id: INT
  -   sender_id: INT
  -   sent_at: TIMESTAMP

```sql
WITH daily_counts AS (
    SELECT 
        DATE(m.sent_at) AS message_date,
        u.user_id,
        u.user_name,
        COUNT(m.sender_id) AS message_count
    FROM npn_messages m
    JOIN npn_users u ON u.user_id = m.sender_id
    GROUP BY DATE(m.sent_at), u.user_id
),
daily_max AS(
  SELECT 
  message_date,
  MAX(message_count) AS max_count
  FROM daily_counts
  GROUP BY message_date
)
SELECT
d.message_date,
d.user_id,
d.user_name,
m.max_count
FROM daily_counts d
JOIN daily_max m 
  ON d.message_date = m.message_date
  AND d.message_count = m.max_count
ORDER BY d.message_date;
```

➡️ Günlük mesaj sayıları CTE ile hesaplanır ve maksimum değerle eşleştirilir.

---

### Day 13 – Naughty or Nice Score Extremes 🟢 Easy

**Question:**
- Santa's audit team is reviewing this year's behavior scores to find the extremes — write a query to return the lowest and highest scores recorded on the Naughty or Nice list.


**Table Schema:**
- Table: behavior_scores
  -   record_id: INTEGER
  -   child_name: VARCHAR
  -   behavior_score: INTEGER

```sql
SELECT MAX(behavior_score), MIN(behavior_score)
FROM behavior_scores;
```

---

## Day 14 – Focus Challenge End Dates 🟡 Medium

**Question:**
- The Productivity Club is tracking members' challenge start dates and wants to calculate each member's focus_end_date, exactly 14 days after their start date. Can you write a query to return the existing table with the focus_end_date column?


**Table Schema:**
- Table: focus_challenges
  -   member_id: INTEGER
  -   member_name: VARCHAR
  -   start_date: DATE

```sql
SELECT member_id, member_name, start_date,
       DATE(start_date,'14 days') AS focus_end_date
      --MySQL: DATE_ADD(start_date, INTERVAL 14 DAY) AS focus_end_date
FROM focus_challenges;
```

---

## Day 15 – The Grinch's Mischief Tracker 🔴 Hard

**Question:**
- The Grinch is tracking his daily mischief scores to see how his behavior changes over time. Can you find how many points his score increased or decreased each day compared to the previous day?


**Table Schema:**
- Table: grinch_mischief_log
  -   log_date: DATE
  -   mischief_score: INTEGER

```sql
SELECT log_date, mischief_score,
       mischief_score - LAG(mischief_score) OVER (ORDER BY log_date) AS score_change
FROM grinch_mischief_log;
```

---

## Day 16 – Cozy Snow Day Tasks 🟢 Easy

**Question:**
- It's a snow day, and Buddy is deciding which tasks he can do from under a blanket. Can you find all tasks that are either marked as 'Work From Home' or 'Low Priority' so he can stay cozy and productive?


**Table Schema:**
- Table: daily_tasks
  -   task_id: INTEGER
  -   task_name: VARCHAR
  -   task_type: VARCHAR
  -   priority: VARCHAR

```sql
SELECT *
FROM daily_tasks
WHERE task_type = 'Work From Home' OR priority = 'Low';
```

---

## Day 17 – Evening Task Categories 🟡 Medium

**Question:**
- During a quiet evening of reflection, Cindy Lou wants to categorize her tasks based on how peaceful they are. Can you write a query that adds a new column classifying each task as 'Calm' if its noise_level is below 50, and 'Chaotic' otherwise?


**Table Schema:**
- Table: evening_tasks
  -   task_id: INTEGER
  -   task_name: VARCHAR
  -   noise_level: INTEGER

```sql
SELECT task_id, task_name,
       CASE WHEN noise_level < 50 THEN 'Calm' ELSE 'Chaotic' END AS peacefulness
FROM evening_tasks;
```

---

## Day 18 – Progress Tracking 🔴 Hard

**Question:**
- Over the 12 days of her data challenge, Data Dawn tracked her daily quiz scores across different subjects. Can you find each subject's first and last recorded score to see how much she improved?


**Table Schema:**
- Table: daily_quiz_scores
  -   subject: VARCHAR
  -   quiz_date: DATE
  -   score: INTEGER

```sql
WITH minmax AS(
SELECT 
subject, 
MIN(quiz_date) AS min_date, 
MAX(quiz_date) AS max_date
FROM daily_quiz_scores
  GROUP BY subject
)
SELECT 
  m.subject,
  m.min_date,
  m.max_date,
  d1.score first_score,
  d2.score last_score
FROM minmax m
  JOIN daily_quiz_scores d1
       ON d1.subject = m.subject AND d1.quiz_date = m.min_date 
JOIN daily_quiz_scores d2
     ON d2.subject = m.subject AND  d2.quiz_date = m.max_date;
```

```sql
-- My Solution 2:
WITH ranked AS (
    SELECT
        subject,
        quiz_date,
        score,
        ROW_NUMBER() OVER (PARTITION BY subject ORDER BY quiz_date) AS rn_first,
        ROW_NUMBER() OVER (PARTITION BY subject ORDER BY quiz_date DESC) AS rn_last
    FROM daily_quiz_scores
)
SELECT
    subject,
    MAX(CASE WHEN rn_first = 1 THEN score END) AS first_score,
    MAX(CASE WHEN rn_last = 1 THEN score END) AS last_score
FROM ranked
GROUP BY subject;
```

➡️ İlk ve son skorlar CTE + JOIN veya `ROW_NUMBER()` ile hesaplanır.

---

## Day 19 – Gift Wrap Paper Usage 🟢 Easy

**Question:**
- Clara is reviewing holiday orders to uncover hidden patterns — can you return the total amount of wrapping paper used for orders that were both gift-wrapped and successfully delivered?


**Table Schema:**
- Table: holiday_orders
  -   order_id: INT
  -   customer_name: VARCHAR
  -   gift_wrap: BOOLEAN
  -   paper_used_meters: DECIMAL
  -   delivery_status: VARCHAR
  -   order_date: DATE

```sql
SELECT SUM(paper_used_meters)
FROM holiday_orders
WHERE gift_wrap = 1 AND delivery_status = 'Delivered';
```

---

## Day 20 – Hot Cocoa Break Logs 🟡 Medium

**Question:**
- Jack Frost wants to review all the cocoa breaks he actually took — including the cocoa type and the location he drank it in. How would you combine the necessary tables to show each logged break with its matching cocoa details and location?


**Table Schema:**
- Table: cocoa_logs
  -   log_id: INT
  -   break_id: INT
  -   cocoa_id: INT
  
- Table: break_schedule
  -   break_id: INT
  -   location_id: INT
 
- Table: cocoa_types
  -   cocoa_id: INT
  -   cocoa_name: VARCHAR
 
- Table: locations
  -   location_id: INT
  -   location_name: VARCHAR

```sql
 SELECT b.break_id,l.location_name, t.cocoa_name 
FROM cocoa_logs c
JOIN break_schedule b ON 
    c.break_id=b.break_id
JOIN locations l ON
    b.location_id=l.location_id
JOIN cocoa_types t ON
    c.cocoa_id=t.cocoa_id
```
    
➡️ Çoklu JOIN ile log, lokasyon ve ürün bilgileri birleştirilir.

---

## Day 21 – Fireside Story Running Total 🔴 Hard

**Question:**
- The Snow Queen hosts nightly fireside chats and records how many stories she tells each evening. Can you calculate the running total of stories she has shared over time, in the order they were told?


**Table Schema:**
- Table: story_log
  -   log_date: DATE
  -   stories_shared: INT

```sql
SELECT log_date, stories_shared,
       SUM(stories_shared) OVER (ORDER BY log_date) AS cumulative_total
FROM story_log;
```

---

## Day 22 – Penguin Sleigh Ride Filter 🟢 Easy

**Question:**
- The penguins are signing up for a community sleigh ride, but the organizers need a list of everyone who did NOT choose the "Evening Ride." How would you return all penguins whose selected time is not the evening slot?


**Table Schema:**
- Table: sleigh_ride_signups
  -   signup_id: INT
  -   penguin_name: VARCHAR
  -   ride_time: VARCHAR

```sql
SELECT *
FROM sleigh_ride_signups
WHERE ride_time <> 'Evening';
```

---

## Day 23 – Gingerbread House Top Builders 🟡 Medium

**Question:**
- The Gingerbread House Competition wants to feature the top 3 builders who used the most distinct candy types in their designs. How would you find the builders with the highest count of unique candies, and return only the top three?


**Table Schema:**
- Table: gingerbread_designs
  -   builder_id: INT
  -   builder_name: VARCHAR
  -   candy_type: VARCHAR

```sql
SELECT builder_id, builder_name
FROM gingerbread_designs
GROUP BY builder_id, builder_name
HAVING COUNT(DISTINCT candy_type) >= 4;
```

---

## Day 24 – User Goal Analysis 🔴 Hard

**Question:**
- As the New Year begins, the goals tracker team wants to understand how user types differ. How many completed goals does the average user have in each user_type?


**Table Schema:**
- Table: user_goals
  -   user_id: INT
  -   user_type: VARCHAR
  -   goal_id: INT
  -   goal_status: VARCHAR

```sql
SELECT 
   user_type,
   AVG(completed_goals) AS avg_completed_goals
FROM
  (
  SELECT
    user_id,
    user_type,
    COUNT(goal_status) AS completed_goals
  FROM user_goals
  WHERE goal_status='Completed'
  GROUP BY user_id, user_type
) AS per_user
GROUP BY user_type;
```

➡️ Kullanıcı bazlı tamamlanan hedefler hesaplanıp kullanıcı tipine göre ortalaması alınır.

---

## 🏅 Sertifika

Bu challenge’ı 24 gün boyunca eksiksiz tamamlayarak, InterviewMaster tarafından verilen **SQL Advent Calendar Completion Certificate** almaya hak kazandım. 

<img width="1585" height="1229" alt="image" src="https://github.com/user-attachments/assets/9a79fdec-f931-48e8-9cab-265b7e783657" />

Bu sertifika;  
- Disiplinli ve sürdürülebilir SQL pratik alışkanlığını,  
- Farklı zorluk seviyelerinde problem çözme becerisini,  
- Analitik düşünme ve sorgu tasarımı yetkinliğini  

resmi olarak belgelendirmektedir.


---
## 🔗 Daha Fazla Bilgi
Daha fazla bilgi için  [interviewmaster.ai/advent](https://interviewmaster.ai/advent) adresini ziyaret edin.




