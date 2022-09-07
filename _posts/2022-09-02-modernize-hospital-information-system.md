---
layout: post
title: Modernizing Hospital Information System -Thought Process
published: False
---

Three years into my career I was tasked to build an information system. Actually, it is more of rebuilding since the previous system becomes obselete after the company change into new service dashboard. Up to now, all reporting data has been manually calculated and no automated analytics take place.

The old system was built in 2017. At first glance, the group seems to rush the project as no software development standard are set. The data warehouse are bunch of sql files and database tables' name are coded and no documentations to give overview of the process. The old system uses SQL Server and SSMS to perform full ETL process. 

I did some research from big technology companies on how they implement a good data architecture. This post is about my decisions which was inspired from my readings that has been tailored to the company situation.

To give an overview, there are three important principles that governs the technology choice.

1) Observability
2) Document
3) Performance

## Observability

When moving data from one place to another or performing etl/elt there are possiblity that error happens. What is more frustrating is that some error is not apparent, no error messages are thrown. A good example is data error. The data source database is maintained by outside party. So, when there are changes, it affects the pipeline as a whole. I've encountered a problem where table join results in wrong data because of change made to the primary key. 

To solve this, pipeline process and database (warehouse) monitoring should be split. Warehouse data is to be considered final. Sanity check should only happen at the pipeline stage.
