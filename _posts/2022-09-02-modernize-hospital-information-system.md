---
layout: post
title: Modernizing Hospital Information System -Thought Process
---

I was fortunate to join a healthcare company in the middle of this year. My hopes are high as I was informed during the interview that my role focuses a lot on building data systems.

One week after I joined, I was bewildered by the amount of work needed. The problems are these:

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

This list summarize the problem.

1) No documentation
2) Slow query
3) Unreliable data warehouse

A data-driven organization does not dependent on engineers. As an engineer, building a system is not a problem. A leader have to realize that modern technology is only a tool to serve the company. That means, if a tool does not make sense for the organization's goal it will be a bad investment. 

Modern data stack can be described by these two aspects - fast and reliable. As decision need to be real-time as possible, different technology emerge to serve the purpose. However, to move an entire stack, one should make the approach one by one. 

For that reason, I come up with 3 milestones that act as a standard of the end product. This way, the team is able to sel-check as we progress to the new information system.

1) Ability to observe the ETL process-Is my fact table, correct?
2) Not every ETL process needs to be fast- Which process is essential?
3) Think SDLC-Code standard, versioning, self-explanatory (almost)

One thing I need to keep in mind, is that, standards might change as the data source is stil in hot development. But it is definitely a good starting point for the team!
