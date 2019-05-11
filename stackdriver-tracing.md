# Tracing and Metrics with OpenCensus and Google Cloud/StackDriver

*Wouldn't it be useful to be able to easily identify the bottleneck in your web application, or processes?  Even better, to be alerted when there is a change in the baseline performance?  Perhaps you are a Site Reliability Engineer (SRE) and need a Service Level Indicator (SLI) to determine if you are meeting your customers expectations.* 

This article will explain how you can do this, using OpenCensus and StackDriver tracing within Google Cloud.

## What is tracing anyway?

A trace is a description or visualization which shows how a request flows through a system.  It typically includes data like processing time (latency) at various stages, enabling a waterfall view of the complete processing time, similar to the view provided by developer tools within a web browser like Google Chrome.

