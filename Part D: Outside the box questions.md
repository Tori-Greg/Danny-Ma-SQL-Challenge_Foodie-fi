# D. Outside the box questions
The following are open ended questions which might be asked during a technical interview for this case study - there are no right or wrong answers, but answers that make sense from both a technical and a business perspective make an amazing impression!

##### 1. How would you calculate the rate of growth for Foodie-Fi?
I intend to determine the growth rate for Foodie-Fi by computing organic revenue growth. This approach offers a holistic view of how our products are perceived by customers and pinpoints areas where revenue enhancement is required, particularly when contrasting the periods of 2020 and 2021.

__Query__

    WITH CTE AS (
        SELECT 
            SUM(CASE WHEN EXTRACT(YEAR FROM Start_date) = 2020 THEN Price ELSE 0 END) AS yr_2020,
            SUM(CASE WHEN EXTRACT(YEAR FROM Start_date) = 2021 THEN Price ELSE 0 END) AS yr_2021
        FROM Foodie_fi.Subscriptions AS FS
        JOIN Foodie_fi.Plans AS FP ON FS.Plan_id = FP.Plan_id
    )
    SELECT ROUND(((yr_2021 / yr_2020) - 1) * 100, 2) AS revenue_growth_rate
    FROM CTE;

__Output:__

| revenue_growth_rate |
| ------------------- |
| -74.27              |

Our analysis indicates a substantial decrease of 74% in our revenue from 2020 to 2021. This decline could be attributed to various factors such as changes in customer preferences, market dynamics, or shifts in the competitive landscape.

---
