# Operation_Analytics_and_Investigating_Metric_Spike

SQL Queries:
Question 1:  Calculate the number of jobs reviewed per hour per day for November 2020?
Syntax:
select job_id, ds as dates, round(count(job_id)/sum(time_spent)*3600) as 'job reviewed per hour per day' from job_data
group by 2,1

Question-2: Calculate 7 day rolling average of throughput? For throughput, do you prefer daily metric or 7-day rolling and why?
Syntax:
select  round(count(event)/sum(time_spent),2) as weekly_throughput from job_data;
Output: 
weekly_throughput= 0.03
select ds as dates,event, round(count(event)/sum(time_spent),2) as daily_throughput from job_data
group by 1,2 order by 1;

Question 3: Calculate the percentage share of each language in the last 30 days?
Syntax:  
select time_spent,language as languages ,
round (( (100*count(distinct(language))/sum(time_spent))))  as percentage from job_data
group by 1,2;

Question 4: Let’s say you see some duplicate rows in the data. How will you display duplicates from the table?

Syntax:
select job_id,count(*) as duplicate_counts
from job_data
group by job_id having count(*) >1

Queston5: Calculate the weekly user engagement?
Syntax: 
select extract( week FROM occurred_at) as week_num ,
count(distinct user_id) as weekly_active_users from events
where event_type ='engagement'
group by week_num;

Question 6:  Calculate the user growth for product?
Syntax:
select monthnum, usercount, round(((usercount/lag(usercount,1) over 
(order by monthnum) -1)*100),2) as 'growth%' from
(select extract( month from created_at) as monthnum,
count(activated_at) as usercount
from users
where activated_at not in ("")
group by monthnum
order by monthnum ) a;

Question 7: Calculate the weekly retention of users-sign up cohort?
Syntax: 

select firstloginweek as "weeknumber",
sum(case when week_number=0 then 1 else 0 end) as "week 0",
sum(case when week_number=1 then 1 else 0 end) as "week 1",
sum(case when week_number=2 then 1 else 0 end) as "week 2",
sum(case when week_number=3 then 1 else 0 end) as "week 3",
sum(case when week_number=4 then 1 else 0 end) as "week 4",
sum(case when week_number=5 then 1 else 0 end) as "week 5",
sum(case when week_number=6 then 1 else 0 end) as "week 6",
sum(case when week_number=7 then 1 else 0 end) as "week 7",
sum(case when week_number=8 then 1 else 0 end) as "week 8",
sum(case when week_number=9 then 1 else 0 end) as "week 9",
sum(case when week_number=10 then 1 else 0 end) as "week 10",
sum(case when week_number=11 then 1 else 0 end) as "week 11",
sum(case when week_number=12 then 1 else 0 end) as "week 12",
sum(case when week_number=13 then 1 else 0 end) as "week 13",
sum(case when week_number=14 then 1 else 0 end) as "week 14"
from
( select a.user_id, a.loginweek,b.firstloginweek,a.loginweek-b.firstloginweek as week_number
from
(select user_id,extract( week from occurred_at) as loginweek 
from events group by 1,2) a,
(select user_id, min(extract(week from occurred_at) ) as firstloginweek
from events group by 1) b
where a.user_id=b.user_id) c
group by firstloginweek order by firstloginweek;

Question 8: Calculate the weekly engagement per device?
Syntax:
 select 
extract(week from occurred_at) as week,
count( device) as device_num,
count( distinct user_id) as user_num,
round(count(device)/count(distinct user_id)) as device_per_user
from events
where event_type ='engagement'
group by week;

Question 9: Calculate the email engagement metrics?
Syntax: 
 select extract( week from occurred_at) as week,
count( case when action = 'sent_weekly_digest' then user_id else null end) as weekly_digest,
count( case when action = 'email_open' then user_id else null end) as email_open,
count( case when action = 'email_clickthrough' then user_id else null end) as email_clickthrough,
count( case when action = 'sent_reengagement_email' then user_id else null end) as sent_reengagement_email,
count(user_id) as total from email_events group by 1;



