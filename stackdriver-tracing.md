# Tracing and Metrics with OpenCensus and Google Cloud/StackDriver

*Wouldn't it be useful to be able to easily identify the bottleneck in your web application, or processes?  Even better, to be alerted when there is a change in the baseline performance?  Perhaps you are a Site Reliability Engineer (SRE) and need a Service Level Indicator (SLI) to determine if you are meeting your customers expectations.* 

This article will explain how you can do this, using OpenCensus and StackDriver tracing within Google Cloud.

## What is tracing anyway?

A trace is a description or visualization which shows how a request flows through the various components of a system.  It typically includes data like processing time (latency) at various stages, enabling a waterfall view of the complete processing time, similar to the view provided by developer tools within a web browser like Google Chrome.

![Google Chrome waterfall chart](https://github.com/pmoorey/articles/blob/master/img/tracing/chrome-waterfall.png)

## What compontents are involved in tracing?

## Tracing for IT automation processes

I frequently develop software solutions to automate IT processes in the domain of computer networking.  I explored how I can gain visibility into the performance and reliability of the automation using tracing.  This enables me to be alerted when a problem occurs with a particular process, such as a failure or performance change.

A typical script may involve the several stages:

- Retrieve data from master data source
- Retrieve data from IT system
- Analyze data in both systems
- Perform CRUD operations in IT system (multiple iterations)
- Generate report of changes
