# Tracing and Metrics with OpenCensus and Google Cloud/StackDriver

## Overview
*Wouldn't it be useful to easily identify the bottleneck in your web application, or processes?  Even better, be alerted when there is a change in the baseline performance?  Perhaps you are a Site Reliability Engineer (SRE) and need a Service Level Indicator (SLI) to determine if customers expectations are being met?.* 

This article will explain how you can do this, using OpenCensus and StackDriver tracing within Google Cloud.

## What is tracing anyway?

A trace is a description or visualization which shows how a request flows through the various components of a system.  It typically includes data such as processing time (latency) at various stages.  This enables a waterfall view of the processing times, similar to the view provided by developer tools within a web browser like Google Chrome.

_The waterfall chart in Chrome developer tools is conceptually similar to a trace_
![Google Chrome waterfall chart](https://github.com/pmoorey/articles/blob/master/img/tracing/chrome-waterfall.png)

## What components are involved in tracing?

Tracing is enabled within the source code of an application.  A popular tracing library is OpenCensus which originated from Google's internal product called Census.  It integrates with various programming langauges and includes support for exporting traces to various backends including Google StackDriver, Prometheus, SignalFx and Zipkin. 

_Sample architecture diagram for tracing_ 
![Tracing architecture diagram](https://github.com/pmoorey/articles/blob/master/img/tracing/trace-architecture.png)

A trace represents a single request as it flows through a system, it includes one parent 'span', and optionally one or more child spans.  A span represents one or more operations in a trace, for example a database query, HTTP request to an API, or function within the source code.

_Example trace for a web request, which includes multiple spans for various operations_ 
![Tracing architecture diagram](https://github.com/pmoorey/articles/blob/master/img/tracing/trace-example.png)

## Tracing for IT automation processes

I frequently develop software solutions to automate IT processes in the domain of computer networking.  This led to me explore how I can gain visibility into the performance and reliability of the automation using tracing.  Tracing enables alerting if a problem occurs with a particular process, such as a failure or performance change.

A typical automation process may involve the several stages, for example:
- Retrieving data from master data source
- Retrieving data from IT system
- Analyzing data in both systems
- Performing CRUD operations in IT system (multiple iterations)
- Generating report of changes

I'll show how to enabling tracing for an IT process within a simple Python script, including sending traces to StackDriver in Google Cloud Platform (GCP) and creating metrics.

### Prerequisites
The following items are required to implement the solution:
- Google Cloud project, with service account (Cloud Trace Agent IAM role assigned)
- Python libaries for OpenCensus, including exporter for StackDriver
- Source code (the process to enable tracing for)

### Google Cloud Project and service account
The Google Cloud project is used to store and visualize trace data sent by the OpenCensus libraries, as well as create metrics and reports. You can request a personal Google Cloud account with limited free credits.  Create a service account and assign the 'Cloud Trace Agent' Identity and Access Management (IAM) role.  Create and download a key; this JSON file will be used by OpenCensus to authenticate to the Google Cloud project.

### Python libaries for OpenCensus

Download the Python libraries using PIP:
```
pip install opencensus
pip install opencensus-ext-stackdriver
```

### Source Code

Import the Python modules required to collect traces and export data to StackDriver

```
# import OpenCensus modules
from opencensus.common.transports.async_ import AsyncTransport
from opencensus.ext.stackdriver import trace_exporter as stackdriver_exporter
from opencensus.trace import tracer as tracer_module

def get_master_data()
    # creates first child span, everything executing within the function is measured
    with tracer.span(name="/it-process/get-master-data") as span:
        data = some_function_to_get_master_data()
    return data

def get_it_system_data()
    # creates second child span, everything executing within the function is measured
    with tracer.span(name="/it-process/get-it-system-data") as span:
        data = some_function_to_get_it_system_data()
    return data

def sync_data(master_data, it_system_data)
    results = []
    for item in data:
        # creates third child span for each item, everything executed in the block below is measured
        with tracer.span(name="/it-process/sync-data") as span:
            response = some_function_to_sync_data()
            results.append(response)
           
            # create an annotation to show what item is being processed
            span.add_annotation("processing item {}".format(item))
    return results

def generate_report(results)
    # creates fourth child span for each item, everything executed in the block below is measured
    with tracer.span(name="/it-process/generate-report") as span:
        report = some_function_to_generate_report(item)
    return report


if __name__ == '__main__':

    # setup the OpenCensus trace and exporter
    exporter = stackdriver_exporter.StackdriverExporter(project_id='my-google-project-id', transport=AsyncTransport)
    tracer = tracer_module.Tracer(exporter=exporter)
    
    # create a parent span, everything which executes below this is included in the span 
    with tracer.span(name="/it-process") as span:
        master_data = get_master_data()
        it_system_data = get_it_system_data()
        results = sync_data(master_data, it_system_data)
        generate_report(results)
```






