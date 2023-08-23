# B. Data Analysis Questions
#### 1.  How many customers has Foodie-Fi ever had?
   __Query:__
   
    Select 
    	Count(Distinct Customer_id) as Total_Customers 
    From Foodie_fi.Subscriptions;

__Output:__

| total_customers |
| --------------- |
| 1000            |

---

#### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value.
__Query:__

    SELECT
        EXTRACT(MONTH FROM start_date) AS Month_No,
        TO_CHAR(start_date, 'Month') AS Month_name,
        Plan_name,
        COUNT(plan_name) AS frequency
    FROM
        Foodie_fi.Subscriptions
    FULL OUTER JOIN
        Foodie_fi.Plans
    ON
        Subscriptions.Plan_id = Plans.Plan_id
    WHERE
        Plan_name = 'trial'
    GROUP BY
        Plan_name,
        EXTRACT(MONTH FROM start_date),
        TO_CHAR(start_date, 'Month')
    ORDER BY
        EXTRACT(MONTH FROM start_date);

__Output:__

| month_no | month_name | plan_name | frequency |
| -------- | ---------- | --------- | --------- |
| 1        | January    | trial     | 88        |
| 2        | February   | trial     | 68        |
| 3        | March      | trial     | 94        |
| 4        | April      | trial     | 81        |
| 5        | May        | trial     | 88        |
| 6        | June       | trial     | 79        |
| 7        | July       | trial     | 89        |
| 8        | August     | trial     | 88        |
| 9        | September  | trial     | 87        |
| 10       | October    | trial     | 79        |
| 11       | November   | trial     | 75        |
| 12       | December   | trial     | 84        |

This code provides insights into the popularity and distribution of trial plan sign-ups over different months, aiding in understanding user engagement trends.

---

#### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name



__Query:__

    SELECT
        EXTRACT(YEAR FROM Start_date) AS Yr,
        Plan_name,
        COUNT(Plan_name) AS Countofevents
    FROM
        Foodie_fi.Subscriptions
    FULL OUTER JOIN
        Foodie_fi.Plans
    ON
        Subscriptions.Plan_id = Plans.Plan_id
    WHERE
        EXTRACT(YEAR FROM Start_date) > 2020
    GROUP BY
        EXTRACT(YEAR FROM Start_date),
        Plan_name
    ORDER BY
        Countofevents DESC;

__Output:__

| yr   | plan_name     | countofevents |
| ---- | ------------- | ------------- |
| 2021 | churn         | 71            |
| 2021 | pro annual    | 63            |
| 2021 | pro monthly   | 60            |
| 2021 | basic monthly | 8             |

This query examines subscription data in the context of the "Foodie_fi" service. By combining information from the "Subscriptions" and "Plans" tables, it compiles details about different subscription plans. The query computes the frequency of subscription events for each plan and year, concentrating on events that occurred after 2020. The outcomes are then arranged based on the count of events in descending order, unveiling trends in plan popularity over more recent years.

---












