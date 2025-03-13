# Data Analyst Job Market: Salary & Skill Insights

This project explores the landscape of data analyst roles, focusing on identifying the highest-paying positions, the skills employers demand, and where these two factors intersect for optimal career growth.

🔍 **SQL queries? Check them out here:** [project_sql folder](/project_sql/)

## Project Goal

This project was initiated to provide a clearer understanding of the data analyst job market, specifically to pinpoint the skills that lead to the highest salaries and are most sought after by employers. The aim is to empower individuals to make informed career decisions.

The project answers the following key questions:

* Which data analyst jobs offer the highest compensation?
* What skills are essential for these top-paying roles?
* Which skills are most frequently requested in data analyst job postings?
* How do different skills correlate with average salaries?
* Which skills offer the best combination of high demand and high pay?

## Tools Used

The analysis was conducted using a suite of tools designed for data manipulation, analysis, and version control:

* **SQL:** Used for querying and extracting relevant information from the database.
* **PostgreSQL:** The database management system used to store and manage the job posting data.
* **Visual Studio Code:** The primary environment for writing and executing SQL queries and managing the project.
* **Git & GitHub:** Used for version control, collaboration, and sharing the project's code and analysis.

## Analysis Methodology

The analysis was structured around a series of SQL queries, each designed to address a specific aspect of the data analyst job market:


### 1. Top Paying Data Analyst Jobs

To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;


```

### 2. Skills for Top Paying Jobs

This query examined the skills listed in job postings for the highest-paying positions to identify the most valued expertise.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_title,
        job_id,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)
SELECT top_paying_jobs.*,
    skills
FROM
    top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```
### 3. Most In-Demand Data Analyst Skills

This query identified the skills most frequently mentioned in data analyst job postings.

```sql
SELECT
    skills,
    COUNT (skills_job_dim.skill_id) AS demand_count
FROM 
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = 'True'
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;

```
### 4. Skills Associated with Higher Salaries

This query explored the average salaries associated with different skills to identify high-value expertise.

```sql
SELECT
    skills,
    ROUND (AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = 'True'
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```
### 5. Optimal Skills to Learn

This query combined insights from demand and salary data to identify skills that offer the best return on investment.

```sql
WITH skills_demands AS (
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT (skills_job_dim.skill_id) AS demand_count
FROM 
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL AND
    job_work_from_home = 'True'
GROUP BY
    skills_dim.skill_id
), avg_salary AS (
SELECT
    skills_job_dim.skill_id,
    ROUND (AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = 'True'
GROUP BY
    skills_job_dim.skill_id
)
```
## Key findings:

- Cloud tools and programming languages are very optimal.
- SQL is still very optimal.

## Key Learnings
- Advanced SQL skills are crucial for high-paying data analyst roles.
- Skills in big data, machine learning, and cloud computing significantly boost earning potential.
- Continuous learning and adaptation are essential for success in the evolving data analytics field.

## Conclusion
This analysis provides valuable insights into the data analyst job market, highlighting the skills that are most in demand and associated with the highest salaries. By focusing on these skills, aspiring and current data analysts can enhance their career prospects and maximize their earning potential.
