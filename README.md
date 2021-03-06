# Epimetheus  - fork
This is a customized fork of https://github.com/roylines/node-epimetheus

- Only express is supported.
- Histogram has additional buckets
- Summary has the 1.0 quntile added
- Additional metrics beyond HTTP are removed (should be provided by prom-client)
- instrumentWithClient allows you to provide a client to add metrics to

# Instrumentation
Epimetheus automatically measures a number of metrics once instrumented. There are 3 categories of instrumentation measured: [response duration](#duration), [event loop lag](#lag) and [memory](#memory). See below for details on each.
The following metrics are instrumented via the /metrics endpoint:

## <a name="duration"></a> Duration Metrics
There are two metrics measuring request duration:

- **http\_request\_duration\_seconds (summary)**: a [summary](https://prometheus.io/docs/concepts/metric_types/#summary) metric measuring the duration in seconds of all requests. It can be used to [calculate average request durations](https://prometheus.io/docs/practices/histograms/#count-and-sum-of-observations).
- **http\_request\_buckets\_seconds (histogram)**: a [histogram](https://prometheus.io/docs/concepts/metric_types/#histogram) metric used to count duration in buckets of sizes 500ms and 2000ms. This can be used to [calculate apdex](https://prometheus.io/docs/practices/histograms/#apdex-score) using a response time threshold of 500ms.

In each case, the following [labels](https://prometheus.io/docs/practices/naming/#labels) are used:

- **status**: the http status code of the response, e.g. 200, 500
- **method**: the http method of the request, e.g. put, post.
- **handler**: the handler of the request.

# Installation
```
> npm install --save epimetheus
```
See examples below for examples of use with [express](#express).

# <a name="express"></a> Express
```
const client = require('prom-client');
const express = require('express');
const epimetheus = require('epimetheus');

const app = express();
epimetheus.instrumentWithClient(app,client);

var myCounter = new client.Counter('thing_something_requests_total','Count of something requests')

app.get('/', (req, res) => {
  myCoutner.inc()
  res.send();
});

app.listen(3000, () => {
  console.log('express server listening on port 3000');
});

```
# Try It Out
The docker-compose.yml file in the examples directory will create a prometheus server and an example each of an [http](#http), [express](#express), [hapi](#hapi) and [restify](#restify) server.

Assuming you have installed [docker](https://docs.docker.com) and [docker-compose](https://docs.docker.com/compose/install/), you can try it out by doing the following:

```
> cd examples
> docker-compose up
```

You can then view the prometheus server on [http://127.0.0.1:9090](http://127.0.0.1:9090)

# Etymology

![Epimetheus](http://www.greekmythology.com/images/mythology/epimetheus_28.jpg)

Epimetheus was one of the Titans and the brother of Prometheus
His name is derived from the Greek word meaning 'afterthought',
which is the antonym of his brother's name, Prometheus, meaning 'forethought'.
