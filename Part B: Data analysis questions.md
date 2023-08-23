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

#### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

__Query:__

    WITH CTE AS (
        SELECT
            COUNT(Customer_id) AS Total,
            (
                SELECT COUNT(Plan_name)
                FROM Foodie_fi.Subscriptions
                FULL OUTER JOIN Foodie_fi.Plans
                ON Subscriptions.Plan_id = Plans.Plan_id
                WHERE Plan_name = 'churn'
            ) AS Churn_no
        FROM
            Foodie_fi.Subscriptions
        FULL OUTER JOIN
            Foodie_fi.Plans
        ON
            Subscriptions.Plan_id = Plans.Plan_id
    )
    SELECT
        Churn_no,
        Total,
        ROUND(Churn_no::NUMERIC / Total * 100, 1) AS Churn_rate_perc
    FROM
        CTE;

__Output:__

| churn_no | total | churn_rate_perc |
| -------- | ----- | --------------- |
| 307      | 2650  | 11.6            |

This query analyzes subscription data to assess the rate of customer attrition. By creating a temporary data structure called a Common Table Expression (CTE), the query calculates the total count of customers as well as the count of those who have terminated their subscriptions. Through the amalgamation of subscription and plan specifics, the query determines the proportion of customers who have churned compared to the total, rounded to a single decimal point. The outcome provides insights into the speed at which customers are discontinuing their subscriptions, contributing to a more profound comprehension of retention trends.

---

#### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

**Query:**

    WITH CTE AS (
        SELECT *,
            LAG(plan_id, 1) OVER (PARTITION BY Customer_id ORDER BY Plan_id) AS Prevplan
        FROM Foodie_fi.Subscriptions
    )
    SELECT
        COUNT(Prevplan) AS Churn_no,
        ROUND(COUNT(*) * 100 / (SELECT COUNT(DISTINCT customer_id) FROM Foodie_fi.Subscriptions), 0) AS Churn_Perc
    FROM CTE
    WHERE Plan_id = 4 AND Prevplan = 0;

__Output:__

| churn_no | churn_perc |
| -------- | ---------- |
| 92       | 9          |


This query employs a Common Table Expression (CTE) to analyze subscription data. It calculates the count and percentage of customers who have churned immediately after their initial free trial. The churn percentage is determined based on the total count of distinct customer IDs who took part in the trial. This analysis provides insight into the proportion of customers who decide not to continue after their trial period, rounded to the nearest whole number.

---

#### 6. What is the number and percentage of customer plans after their initial free trial?

**Query:**

    WITH CTE AS (
        SELECT
            S.Plan_id,
            Plan_name,
            COUNT(S.Plan_id) AS Noofplans,
            SUM(COUNT(S.Plan_id)) OVER ()::NUMERIC AS Total
        FROM
            Foodie_fi.Subscriptions AS S
            FULL OUTER JOIN Foodie_fi.Plans AS P ON S.Plan_id = P.Plan_id 
        WHERE
            S.Plan_id <> 4
        GROUP BY
            S.Plan_id, Plan_name
    )
    SELECT
        Plan_id,
        Plan_name,
        Noofplans,
        Total,
        ROUND((Noofplans / Total) * 100, 0) AS Plansperc
    FROM
        CTE
    ORDER BY
        Plan_id;

| plan_id | plan_name     | noofplans | total | plansperc |
| ------- | ------------- | --------- | ----- | --------- |
| 0       | trial         | 1000      | 2343  | 43        |
| 1       | basic monthly | 546       | 2343  | 23        |
| 2       | pro monthly   | 539       | 2343  | 23        |
| 3       | pro annual    | 258       | 2343  | 11        |

This query utilizes a Common Table Expression (CTE) to analyze subscription data. It calculates the proportion of various plans based on their individual counts and the total count of plans, excluding a specific plan with ID 4. By combining subscription and plan details, the query determines the ratio of each plan's count to the overall count, rounded to the nearest whole number. The purpose of this query is to offer insights into the relative adoption rates of different plans, aiding in the comprehension of plan popularity compared to others.

---

#### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?


**Query:**

    WITH CTE AS (
        SELECT
            S.Plan_id,
            Plan_name,
            COUNT(Customer_id) AS customerscount,
            SUM(COUNT(Customer_id)) OVER ()::NUMERIC AS Total
        FROM
            Foodie_fi.Subscriptions AS S
            FULL OUTER JOIN Foodie_fi.Plans AS P ON S.Plan_id = P.Plan_id 
        WHERE
            Start_date = '2020-12-31'
        GROUP BY
            S.Plan_id, Plan_name
    )
    SELECT
        Plan_id,
        Plan_name,
        customerscount,
        Total,
        ROUND((customerscount / Total) * 100)::NUMERIC AS Customersperc
    FROM
        CTE
    GROUP BY
        Plan_id, Plan_name, customerscount, Total
    ORDER BY
        Plan_id;

__Output:__

| plan_id | plan_name | customerscount | total | customersperc |
| ------- | --------- | -------------- | ----- | ------------- |
| 4       | churn     | 1              | 1     | 100           |

This query utilizes a Common Table Expression (CTE) to examine subscription data. It determines the distribution of customers across various subscription plans by calculating the percentage each plan contributes to the total customer count. By combining information from subscriptions and plans, the query computes the proportional representation of customers for each plan compared to the overall number of customers. The query is specifically targeted at a particular starting date ('2020-12-31'). Its objective is to offer insights into the customer distribution across plans on that specific date, aiding in the comprehension of how different plans are performing in relation to each other.

---

#### 8. How many customers have upgraded to an annual plan in 2020?

**Query:**

    SELECT COUNT(Customer_id) AS customerscount
    FROM Foodie_fi.Subscriptions AS S
        FULL OUTER JOIN Foodie_fi.Plans AS P ON S.Plan_id = P.Plan_id 
    WHERE EXTRACT(YEAR FROM Start_date) = 2020 AND Plan_name ILIKE '%annual%';

__Output:__

| customerscount |
| -------------- |
| 195            |

This query examines subscription data. It determines the count of customers who have subscribed to plans containing "annual" in their names, with a focus on subscriptions initiated in the year 2020. The query's purpose is to shed light on the number of customers who chose annual plans during that specific year, contributing to a better understanding of the preference for such plans during that period.

---

#### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

    With annualplan as
     (
	    Select Customer_id, Start_date as annualplandate
	    From Foodie_fi..Subscriptions
	    Where Plan_id = 3),
       Trialplan as 
       (
	    Select Customer_id, Start_date as joindate
	    From Foodie_fi..Subscriptions
	    Where Plan_id = 0
       )
       Select Avg(DATEDIFF(Day, joindate, annualplandate)) as Avgdays
       From annualplan
       join Trialplan on annualplan.Customer_id = Trialplan.Customer_id;

__Output:__

| Avgdays |
| -------------- |
| 104            |

---

#### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)

---

#### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
**Query:**

    SELECT COUNT(Customer_id) AS downgrade
    FROM Foodie_fi.Subscriptions AS S
        FULL OUTER JOIN Foodie_fi.Plans AS P ON S.Plan_id = P.Plan_id 
    WHERE EXTRACT(YEAR FROM start_date) = 2020 AND S.plan_id > 1 AND S.Plan_id < 2;

__Output:__

| downgrade |
| --------- |
| 0         |

This query analyzes subscription data to determine the count of customers who downgraded their plans. It focuses on subscriptions initiated in the year 2020 with plan IDs greater than 1 and less than 2. The query's purpose is to provide insight into the number of customers who switched to a lower plan during that specific year, assisting in understanding customer behavior related to plan changes.

---














