---
layout: post
title: Making a robust and reliable hospital information system
---

Three years into my career, I was tasked to build an information system. Actually, it is more of rebuilding since the previous system becomes obsolete after the company change into a new medical record system. Up to now, all reporting data has been manually calculated, and no automated analytics have taken place.

The old system was built in 2017. The group seems to rush the project as no software development standards are set. The data warehouse is a bunch of SQL files and database naming are coded, and there is no documentation to give an overview of the process. The old system used SQL Server and SSMS to perform ETL processes and warehousing. 

I researched big technology companies on how they implement a good data architecture. This post is about my decisions which were inspired by my readings that have been tailored to the company situation.

To give an overview, three important principles govern the technology choice.

1) Observability
2) Documentation
3) Performance

## Observability

There is always a possibility of error when moving and processing data. Even more frustrating is that some error isn't obvious because no error messages are thrown. One example is when data is wrong. An outside party manages the data source database. So, when something changes, it affects the entire pipeline. For instance, I've run into a problem where a table join gives me incorrect data because the primary key was changed.

To fix this, monitoring of pipeline processes and databases (warehouses) should be separated. Warehouse data should be treated as final. Sanity checks should only happen at the pipeline stage. In the old system, ETL processes were done as cron jobs for stored procedures. Only at the destination does a sanity check happen.

We've decided to replace stored procedure-based ETL with Python jobs. The chosen framework is the Dagster. It is a scheduler with the power of logging for observability. Another important reason is that it can run smoothly on Windows server 2016, the primary operating system for all VMs in this company.

## Documentation

One trend that has become prominent in software development nowadays is versioning. For data systems, the reason is reproducibility. Having good documentation helps shorten debugging time, which enhances the capability of a data team to increase data reliability. But, unfortunately, it is tough to "remember" all columns when the database gets big.

A simple solution to this is to standardize table and column naming. There is no set of rules for this, as per my research. So, it depends on the team. For example:

| Prefix | Description      | Example                                                                 |
|--------|------------------|-------------------------------------------------------------------------|
| fact_  | Fact table       | fact_daily_visits, fact_daily_lab_result                                |
| dim_   | Dimension table  | dim_dates                                                               |
| _ts    | Timestamp column | registration_ts                                                         |
| _id    | Foreign key      | patient_id as foreign key. In dim_patient the primary key is named "id" |

Other than the database, the process should also be documented. This is done by using version control. You may be surprised that there is no version control in place for the old system. So, I use what is familiar to me - Github.

Another realization that I had from my previous job is a good framework is when it promotes a proper software development process. Dagster does an excellent job with the boilerplate code generated through CLI. It introduces a specific form of code structure to help with development flow. Some people may not like this, but I think it is good to help promote standards across teams, especially if multiple engineers are working on the same problems. But one thing I like the most is it comes with the testing module in the boilerplate code. Because we are using Python, we document all packages (with versions) using yml file included in the version control. You may think - Why not docker? As I mentioned, our in-house VMs are old. I did think of using Ubuntu. But since all databases use SQL Server, we do not want to lose some functionality of a native application, especially Security.

So, our MVP project directory looks like this. Inside the data_pipeline module are all the jobs required.

![image](https://user-images.githubusercontent.com/49480914/188824531-a7dc2fd7-cfd3-4ace-8e2b-e54d3c8ba639.png)

## Performance

I remember once during a meeting where a person tries to query the whole year's data, and it takes 10 minutes to give the result. It is not a good experience. Few things can be done here. One is changing VM's processor to a better version to let the database engine perform to its fullest potential, and the second is enhancing pipeline processes.

We focus on the second approach since changing the processor is expensive. One thing we note from the old script is that it processes data from multiple tables sequentially. Doing this definitely will slow down the entire process. Investigating deeper, some calculation steps are a constant derived from another table. In other words, it does not have to be calculated every time the script is invoked. A simple example - is the recalculation of minimum and maximum date. This can be done once a day and saved to a table.

So, we start to split out the process. Some can be done in the python pipeline, and some will stay as stored procedures. As a result, we see a massive improvement in the query without affecting the readability and traceability of the pipeline. Plus, doing it this way help to break up the monolithic process to help catch error quickly.

My principle is performance should not affect readability, especially in mission-critical sections. If there is no other way, a detailed document should follow the segment.

## Conclusion

We are far from the optimum system. Building an in-house data pipeline where an outside party manages most data sources is challenging. Limitations are real regarding pulling data and moving faster as a team. Having standards in place to govern every decision is essential. We want to pave the way for the company and team goals to walk together.

