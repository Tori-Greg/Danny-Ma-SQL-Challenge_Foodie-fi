# A. Customer Journey
Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.
Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

---
**Query:**

    SELECT 
        customer_id, 
        subscriptions.plan_id, 
        start_date, 
        plan_name, 
        price 
    FROM 
        foodie_fi.subscriptions
    FULL OUTER JOIN 
        foodie_fi.plans
    ON 
        subscriptions.plan_id = plans.plan_id
    WHERE 
        customer_id IN (1, 2, 11, 13, 15, 16, 18, 19)
    Order by customer_id, plan_id;

__Output:__

| customer_id | plan_id | start_date               | plan_name     | price  |
| ----------- | ------- | ------------------------ | ------------- | ------ |
| 1           | 0       | 2020-08-01T00:00:00.000Z | trial         | 0.00   |
| 1           | 1       | 2020-08-08T00:00:00.000Z | basic monthly | 9.90   |
| 2           | 0       | 2020-09-20T00:00:00.000Z | trial         | 0.00   |
| 2           | 3       | 2020-09-27T00:00:00.000Z | pro annual    | 199.00 |
| 11          | 0       | 2020-11-19T00:00:00.000Z | trial         | 0.00   |
| 11          | 4       | 2020-11-26T00:00:00.000Z | churn         |        |
| 13          | 0       | 2020-12-15T00:00:00.000Z | trial         | 0.00   |
| 13          | 1       | 2020-12-22T00:00:00.000Z | basic monthly | 9.90   |
| 13          | 2       | 2021-03-29T00:00:00.000Z | pro monthly   | 19.90  |
| 15          | 0       | 2020-03-17T00:00:00.000Z | trial         | 0.00   |
| 15          | 2       | 2020-03-24T00:00:00.000Z | pro monthly   | 19.90  |
| 15          | 4       | 2020-04-29T00:00:00.000Z | churn         |        |
| 16          | 0       | 2020-05-31T00:00:00.000Z | trial         | 0.00   |
| 16          | 1       | 2020-06-07T00:00:00.000Z | basic monthly | 9.90   |
| 16          | 3       | 2020-10-21T00:00:00.000Z | pro annual    | 199.00 |
| 18          | 0       | 2020-07-06T00:00:00.000Z | trial         | 0.00   |
| 18          | 2       | 2020-07-13T00:00:00.000Z | pro monthly   | 19.90  |
| 19          | 0       | 2020-06-22T00:00:00.000Z | trial         | 0.00   |
| 19          | 2       | 2020-06-29T00:00:00.000Z | pro monthly   | 19.90  |
| 19          | 3       | 2020-08-29T00:00:00.000Z | pro annual    | 199.00 |

---


## Explanation

The beginning stage of each customer's onboarding procedure includes a trial plan. However, it remains uncertain whether this trial plan is determined by the customer's selection or if Foodie Fi has a predetermined default plan. From the provided sample, it's evident that one in every four customers which accounts for 25% of our total customers subscribed to the annual Pro plan subscription offered by Foodie Fi.

As indicated by our results, every customer initiates with a trial plan, as evidenced by all customers having a trial plan in their subscription. Subsequently, they make a decision to transition, either opting for our basic monthly plan or upgrading to the annual pro plan. This transition is observable in the data, with customer ID 11 standing out as a case of churn, having not upgraded to any of our plans.

---

