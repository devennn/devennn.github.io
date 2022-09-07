---
layout: post
title: Modernizing Hospital Information System
published: False
---

Three years into my career I was tasked to build an information system. Actually, it is more of rebuilding since the previous system becomes obselete after the company change into new service dashboard. Up to now, all reporting data has been manually calculated and no automated analytics take place.

The old system was built in 2017. At first glance, the group seems to rush the project as no software development standard are set. The data warehouse are bunch of sql files and database tables' name are coded and no documentations to give overview of the process. The old system uses SQL Server and SSMS to perform full ETL process. 

I did some research from big technology companies on how they implement a good data architecture. This post is about my decisions which was inspired from my readings that has been tailored to the company situation.

To give an overview, there are three important principles that governs the technology choice.

1) Observability
2) Documentation
3) Performance

## Observability

There is always a possibility of error when moving and processing data. Even more frustrating is that some error isn't obvious because no error messages are thrown. One example is when data is wrong. The data source database is managed by an outside party. So, when something changes, it affects the entire pipeline. I've run into a problem where a table join gives me wrong data because the primary key was changed.

To fix this, monitoring of pipeline processes and databases (warehouses) should be separated. Warehouse data should be treated as final. Sanity checks should only happen at the pipeline stage. In the old system, ETL processes are done as cron jobs for stored procedures. Only at the destination does a sanity check happen.

We've decided to replace stored procedure-based ETL with Python jobs. The chosen framework is the Dagster. It is a scheduler with the power of logging for observability. Another important reason is that it can run smoothly on Windows server 2016, which is the main operating system for all VMs in this company.

## Documentation

One of the trend that becomes prominent in software development world nowadays is versioning. In data system, the reason for it is reproducibility. Not just that having a good documentation helps to shorten debugging time which enhance the capability of a data team to increase data reliability. When database gets big, it is very hard to "remember" all columns.

A simple solution to this is to standardize table and column naming. There are no set of rules for this as per my research. So, it depends on the team. For example:

| Prefix | Description      | Example                                                                 |
|--------|------------------|-------------------------------------------------------------------------|
| fact_  | Fact table       | fact_daily_visits, fact_daily_lab_result                                |
| dim_   | Dimension table  | dim_dates                                                               |
| _ts    | Timestamp column | registration_ts                                                         |
| _id    | Foreign key      | patient_id as foreign key. In dim_patient the primary key is named "id" |

Other than database, process should also be documented. This is done by using version control. You may be surprised that there is no version control in place for the old system. So, I use what is familiar to me - Github.

Another realizations that I had from my previous job is a good framework is when it promotes proper software development process. Dagster does a good job with the boilerplate code generated through cli. It introduce specific form of code writing to help with development flow. Some people may not like this, but I think it is good to helo with promoting standards accross teams. Especially if there are multiple engineers working on the same problems as a whole. But one thins I like the most is it comes with testing module in the boilerplate code. Because we are using Python, we document all packages (with versions) using yml file incuded in the version control. You may think - Why not docker? As I mentioned, our in house vms are old. I did think of using Ubuntu. But since all databases use SQL Server, we do not want to lose some functionality of a native application, especially Security.

So, our MVP project directory looks like this. Inside data_pipeline module are all the jobs required

![image](https://user-images.githubusercontent.com/49480914/188824531-a7dc2fd7-cfd3-4ace-8e2b-e54d3c8ba639.png)

## Performance

I remeber once during a meeting where a person tries to query the whole year data and it takes 10 minutes to give result. It is not a good experience. Few things can be done here. One is that changeing VM's processor to a better version to let database engine perform in its fullest potential. Second, enhancing pipeline process.

We took the second approach very serious. One thing that we note from the old script is it process data from multiple table in sequential manner. Doing this definitely will slow down entire process. Investigating deeper, there are some calculation steps that is actually a constant that is derived from other table. In other words, it does not have to be calculated every single time the script is invoked. A simple example - recalcultion of minimum and maximum date. This can be done once a day and saved to a table

So, we start to split out process. Some can be done in the python pipeline and some will stay as stored procedure. As the result we see massive improvement in the query without affecting the readability and tracebility of the pipeline. Plus doing it this way help to break up monolitic process to help catch error quickly.

My principle is performance should not affect readability. Especially in mission critical section. If there is no other way, a detail document should follow the segment.

## Conclusion

We are far from optimum system. Building in-house data pipeline where most data sources are managed by outside party is challenging. Limitation is real in terms of pulling data and moving faster as a team. Having standards in place to govern every decisions is imporant. We want to pave a way where company and team goals walk together.
