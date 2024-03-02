# Conversion-rate-analysis-using-PostgreSQL
https://medium.com/@ankan.ab21/calculating-free-to-paid-conversion-rate-with-sql-project-a98e48ca7b48
Calculating the Fraction of Students Who Convert to Paying Ones after Starting a Course
Project Overview
The objective is to estimate the free-to-paid conversion rate among students who engage with video content on the 365 platform. This involves working with three tables containing information about students' registration dates, engagement dates, and subscription purchase dates. The project not only focuses on calculating the conversion rate but also includes the analysis of additional key metrics to draw meaningful conclusions from the data.

Exploratory Data Analysis
Insights to uncover from the tables

What is the free-to-paid conversion rate of students who have watched a lecture on the 365 platform?
What is the average duration between the registration date and when a student has watched a lecture for the first time (date of first-time engagement)?
What is the average duration between the date of first-time engagement and when a student purchases a subscription for the first time (date of first-time purchase)?

SELECT 
    ROUND(
        COUNT(DISTINCT CASE WHEN first_date_purchased IS NOT NULL THEN student_id END) * 100.0 /
        COUNT(DISTINCT student_id),
        2
    ) AS conversion_rate,
    ROUND(
        AVG(days_diff_reg_watch)::NUMERIC,
        2
    ) AS av_reg_watch,
    ROUND(
        AVG(days_diff_watch_purch)::NUMERIC,
        2
    ) AS av_watch_purch
FROM (
    SELECT 
    e.student_id,
    i.date_registered,
    MIN(e.date_watched) AS first_date_watched,
    MIN(p.date_purchased) AS first_date_purchased,
    DATE_PART('day', MIN(e.date_watched)::timestamp - MIN(i.date_registered)::timestamp) AS days_diff_reg_watch,
    DATE_PART('day', MIN(p.date_purchased)::timestamp - MIN(e.date_watched)::timestamp) AS days_diff_watch_purch
FROM
    student_engagement e
JOIN
    student_info i ON e.student_id = i.student_id
LEFT JOIN
    student_purchases p ON i.student_id = p.student_id
GROUP BY
    e.student_id,
    
HAVING first_date_purchased IS NULL
OR first_date_watched <= first_date_purchased
) a;

Interpretation
The free-to-paid conversion rate on the 365 platform stands at approximately 11%, indicating that about 11 out of every 100 students who engage with lectures go on to purchase a subscription. While this figure may seem modest initially, a deeper examination reveals various factors influencing this rate. The platform's broad audience, potentially lacking a specific focus on data science enthusiasts, and uncertainties about where to begin in the learning journey could contribute to the relatively low conversion rate. To address these challenges, an onboarding sequence was introduced in August 2023, tailoring learning paths for individual students. However, external factors such as exam periods for students or time constraints for working individuals may still affect the conversion rate. The importance of continuous feedback, identifying shortcomings, and striving for product improvement is emphasized as a crucial aspect of maintaining and enhancing user engagement.

On average, students take between three and four days to begin watching a lecture after registering on the 365 platform. Ideally, early engagement is desired, and the lessons are easily accessible compared to other platform elements.
