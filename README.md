
Welcome to the Data Job Analysis SQL Project!

📊By leveraging SQL, this project highlights top-paying jobs, in-demand skills and helps to choose the optimal career path. 

My SQL queries can be checked out here: [project_sql folder](/project_sql/)

# Background 
While I was studyng SQL, I had an opportunty to use a dataset which is packed with insights on

📌job titles, 
📌salaries, 📌locations, and 📌ssential skills. 

The project itself aimed to demonstrate not only my SQL skills, but also overall workforce dynamics, pinpointing in-demand and top-paying skills. 

### The questions I wanted to answer through my SQL queries were: 

1. What are the top paying data analyst jobs? 
2. What skills are required for these top-paying jobs?
3. What skills are most in-demand for data analysts?
4. Which skills are associated with higher salaries? 
5. What are the most optimal skills to learn? 

# Tools I used 
📈To conduct a critical analysis of the data job market, I have used the power of several tools: 

- **SQL:** this powerful tool allowed me to unearth meaningful observations by quering the database. 
- **PostgreSQL:** The chosen database management system is perfect for handling this type of data. 
- **Visual Studio Code:** My preferred option for executing SQL queries. 
- **Git & GitHub:** Key to sharing my projects, in this case, my SQL scripts and analysis. 

# The analysis 
Each of the 5 queries aimed to explore specific aspects of data analyst job market. My approach to those questions is summarised below: 

### 1. Top Paying Data Analyst Jobs
🧩To identify the highest paying roles, I filtered data analyst positions by average salary and location, focusing on remote jobs. This query highlights top opportunities in this workforce field. 

```sql
SELECT 
    job_id,
    job_title,
    company_dim.name as company_name,
    job_location,
    job_posted_date,
    job_schedule_type,
    salary_year_avg
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' 
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC; 
```
Here is the breakdown of the top data analyst jobs in 2023: 
- **Broad Salary Spectrum:** Top 10 roles span from $184,000 to $650,000, meaning that there is notable salary potential in the field. 
- **Diverse Employers:** SmartAsset, Meta, and AT&T offer the highest salaries. 
- **Job Title Variety:** There are multiple options in roles, as job titles vary from Data Analyst to Director of Analytics.
<img width="712" alt="Screenshot 2025-01-16 at 12 51 39" src="https://github.com/user-attachments/assets/a2b60831-a13a-4b49-8868-43f47e317a88" /> 
Bar graph visualizing the salary for the top 10 salaries for data analysts; ChatGPT generated this graph from my SQL query results

### 2. What skills are required for top paying roles? 
🧩To find out what skills are required for top-paying jobs, I have joined the job postings table with skills data. This provides information on what skills companies value most.  

```sql
WITH top_paying_jobs AS (
    SELECT 
    job_id,
    job_title,
    salary_year_avg
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' 
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg desc

)

SELECT 
    top_paying_jobs.*, 
    skills
FROM 
    top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY 
    salary_year_avg DESC;

```

Here are some discoveries on demanded skills: 
- **SQL** is leading with a count of 8.
- **Python** is not far behind with a count of 7.
- **Tableau** has a count of 6, which is also significant. 

### 3. What are the most in-demand skills for Data Analysts? 
🧩This query highlighted the most commonly requested skills in job postings, guiding attention to areas of high demand.

```sql 
SELECT 
    skills, 
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_work_from_home = True
GROUP BY 
    skills
ORDER BY 
    demand_count DESC; 
```

- Tools like **SQL and Excel** remain the most wanted, implying the need for foundation in data processing and spreadsheet manipulation. 
- **Python, Tableau and Power BI**, which are used for programming and visualization remain essential, emphasizing importance of skills in data storytelling. 

### 4. What are the top paying skills based on salary?
🧩Exploring the average salary that associated with each skill helped identify which skills are best paying. 

```sql
SELECT 
    skills, 
    ROUND(AVG(salary_year_avg), 0) as average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
GROUP BY 
    skills
ORDER BY 
    average_salary DESC; 
``` 

- Analysts who are skilled with **ML tools and Big Data technologies** are in high demand now, revealing that industry values predictive modeling and data processing capabilities. 
- Knowledge in **development and deployment** tools indicates the crossover between data analysis and engineering. 
- The analysis helped to understand the growing need for **cloud computing expertise**, meaning that cloud proficiency is a helful skill for data analytics. 

### 5. What are the most optimal skill to learn?

🧩In order to identify a startegy for job hunting and skills development, I have combined insights from demand and salary data, indicating skills that are in high demand and are top-paying. 

```sql 
WITH skills_demand AS (
    SELECT    
    skills_dim.skill_id, 
    skills_dim.skills, 
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
     AND job_work_from_home = True
     AND salary_year_avg IS NOT NULL
GROUP BY 
    skills_dim.skill_id
ORDER BY 
    demand_count DESC
), 

average_salary AS (
    SELECT 
    skills_job_dim.skill_id,
    ROUND(AVG(salary_year_avg), 0) as average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND  job_work_from_home = True
GROUP BY 
    skills_job_dim.skill_id
ORDER BY 
    average_salary DESC 
)

SELECT 
    skills_demand.skill_id, 
    skills_demand.skills,
    skills_demand.demand_count,
    average_salary.average_salary
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY 
    average_salary DESC, 
    demand_count DESC; 
```

- Proficiency in **Python and R** highly valued and widely available, as their demand counts for 236 and 148, respectively. Average salaries are $101,397 for Python and $100,499 for R. 
- Skills in **cloud tools and technologies** also show high demand and relatively high salaries. 
- **Visualization and BI tools** also highlight the critical role of business intelligence in data analysis. 
- **Database technologies** also show high average salaries and demand, implying the need for data management and storage. 


# What I learned 
During this course and working on my first SQL project I have boosted by analytical skills, developed essential SQL concepts, such as aggregation, cleaning, sorting and filtering data.✅ 

With this knowledge I can now continue to turn questions into actionable SQL queries and derive valuable insights from data! 

# Conclusion 
🔆This project strengthened my SQL skills and offered valuable insights into the data analyst job market. The results of the analysis provide a roadmap for prioritizing skill development and job search strategies. By focusing on in-demand, high-salary skills, aspiring data analysts can better position themselves in a competitive job market. This exploration emphasizes the significance of continuous learning and staying adaptable to emerging trends in the data analytics field.
