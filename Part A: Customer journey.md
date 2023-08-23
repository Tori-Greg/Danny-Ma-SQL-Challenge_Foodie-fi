# A. Customer Journey
Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.
Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

__Query:__

    Select 
      Customer_id, 
	  Subscriptions.Plan_id, 
	  Start_date,
      Plan_name, 
	  Price
    from Foodie_fi..Subscriptions
    Full outer join Foodie_fi..Plans
    on Subscriptions.Plan_id = Plans.Plan_id
    Where Customer_id in (1,2,11,13,15,16,18,19)

__Output:__

