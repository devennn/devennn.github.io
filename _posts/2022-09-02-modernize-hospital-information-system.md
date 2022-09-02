---
layout: post
title: Modernizing Hospital Information System -Thought Process
---

I was fortunate to join a healthcare company in the middle of this year. My hopes are high as I was informed during the interview that my role focuses a lot on building data systems.

A few days after I joined, I was bewildered by the amount of work needed. The problems are these:

1) Hospital just changed to a new medical records system.
2) Previous analysts and engineers have left the company.
3) Because of that, the old system cannot provide reports and analytics to management.

All reporting data has been manually calculated up to now since there is no system in place for it.

And that became my big goal—rebuilding the information system.

I took a tour of the old system codebase. I hoped it wouldn't be complicated to fork and plug in the new data. 

But I have no idea what I am reading. The system uses SQL Server to perform all ETL processes. Each ETL job is a stored procedure. The data is served to stakeholders using SSRS. The table and column names are cryptic. Running SP is immensely slow. SQL plus cryptic column names and slow results make life so hard.

I'm not familiar with SSRS. It looks old. But, if it serves the purpose, I have no problem with it. Maybe I should rebuild using the same tech stack. One of the most significant selling points for using this technology is security. Since the hospital is a heavy Microsoft user, being integrated with the security layer is essential.

One day, someone came to me to ask for data from the old system. I gave them the data, but a colleague came to me saying we could not trust the data. Most of the time, it is wrong. He said to use other data stored in an excel file that was collected manually. 

It shocks me. The system is supposed to provide reliable data. But even the stakeholders are not confident of it. 

This makes me curious about the warehouse and fact table that is used to provide the data. So I make it a high priority to investigate this matter. What I found is shocking. The error rate is around 10%-15% compared to manual counting. Not all the data. Also, it only occurs roughly 20% of the time. But still, these numbers affect the overall performance and reliability of the system.

# The thought process

If I want to summarize the problem, this is the list.

1) No documentation
2) Slow query
3) Unreliable data

What interests me is that these problems also exist in the data pipeline of the previous company I worked for. I understand that part of the problem is because of the tech debt in the current stack.

The data-driven organization is not dependent on engineers. It is everyone. As an engineer, the building is not a problem. I can say, "Let's invest in Databricks." But if it doesn't make sense for the company-wide usage, the latest technology is a waste. 

Planning a roadmap is challenging. The problems need to be tackled one by one. Thus, I came up with a few standards for the new system. 

1) Ability to observe the ETL process-Is my fact table, correct?
2) Not every ETL process needs to be fast- Which process is essential?
3) Think SDLC-Code standard, versioning, self-explanatory (almost)

In my opinion, having the wrong standard is better than having no standard. It helps to focus on what makes sense for the developer and the stakeholders. I am sure that the standard will change as time goes by. But it's better to have it before embarking on the journey!
