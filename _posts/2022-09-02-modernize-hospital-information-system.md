---
title: "Summarizing the thought process of rebuilding healthcare information system"
date: "September 7, 2022"
excerpt: "I was tasked to build an information system"
cover_image: "/images/posts/hospital-information-system.jpeg"
project: ""
---

It was a fantastic opportunity to be entrusted with the development of software systems from the ground up. This blog post is written to summarize my whole thought process from start to finish.

To give an overview, three important principles govern the technology.

1. Observability
2. Documentation
3. Performance

## Observability

There is always a possibility of error when moving and processing data. Even more frustrating is that some errors aren't obvious because no error messages are thrown. One example is when data is wrong. An outside party manages the data source database. So, when something changes, it affects the entire pipeline. For instance, I've run into a problem where a table join gives me incorrect data because the primary key was changed.

To fix this, monitoring of pipeline processes and databases (warehouses) should be separated. Warehouse data should be treated as final. Sanity checks should only happen at the pipeline stage. In the old system, ETL processes were done using scheduled stored procedures. Only at the destination does a sanity check happen.

We've decided to replace stored procedure-based ETL with Python jobs. The chosen framework is the Dagster. It is a scheduler with the power of logging for observability. Another important reason is that it can run smoothly on Windows Server 2016, the primary operating system for all VMs in this company.

## Documentation

In software development, versioning is important, especially because we want to build a reproducible system. Having good documentation helps shorten debugging time, which enhances the capability of a data team to increase data reliability.

A simple solution to this is to standardize table and column naming. There is no set of rules for this, as per my research. So, it depends on the team. For example:

| Prefix | Description      | Example                                                                 |
| ------ | ---------------- | ----------------------------------------------------------------------- |
| fact\_ | Fact table       | fact_daily_visits, fact_daily_lab_result                                |
| dim\_  | Dimension table  | dim_dates                                                               |
| \_ts   | Timestamp column | registration_ts                                                         |
| \_id   | Foreign key      | patient_id as foreign key. In dim_patient the primary key is named "id" |

Other than the database, the process should also be documented using version control. We decide on Github.

A good framework promotes a proper software development process. Dagster does an excellent job with the boilerplate code generated through the CLI. It introduces a specific form of code structure to help with development flow. It helps to promote standards across teams, especially if multiple engineers are working on the same problems.

So, our MVP project directory looks like this. Inside the data_pipeline module are all the jobs required.

<!-- ![image](https://user-images.githubusercontent.com/49480914/188824531-a7dc2fd7-cfd3-4ace-8e2b-e54d3c8ba639.png) -->

<img src="https://user-images.githubusercontent.com/49480914/188824531-a7dc2fd7-cfd3-4ace-8e2b-e54d3c8ba639.png"  width="30%" height="30%">

## Performance

To enhance performance, 2 basic things can be done. One is upgrading VM's processor to let the database engine perform to its fullest potential, and the second is enhancing pipeline processes.

We focus on the second approach by inspecting SQL script performance. One thing we note is that some calculation steps are referencing multiple tables to produce a constant. Since it is a constant, the calculation can be done once a day and saved to a table.

For much more complex processes, we start to break the script by splitting processes into Python(run on Dagster) and SQL scripts. As a result, we see a massive improvement in the query without affecting the readability and traceability of the pipeline. Plus, doing it this way helps break up the monolithic process to help catch errors quickly.

## Conclusion

We are far from done. Building an in-house data pipeline where an outside party manages most data sources is challenging. Having standards in place to govern every decision is essential. We want to pave the way for the company and team goals to walk together.
