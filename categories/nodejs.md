::: {#main .section}
::: {#page}
::: {.topic_content}
::: {style="text-align:right"}
::: {style="text-align:right"}
Versions \| [v1.0 (td-agent3)](/v1.0/articles/nodejs) \| ***v0.12*
(td-agent2) **
:::
:::

------------------------------------------------------------------------

Centralize Logs from Node.js Applications
=========================================

The '[fluent-logger-node](https://github.com/fluent/fluent-logger-node)'
library is used to post records from Node.js applications to Fluentd.

This article explains how to use the fluent-logger-node library.

[]{#prerequisites}

::: {#table-of-contents .section}
### Table of Contents

[Prerequisites](#prerequisites)

[Installing Fluentd](#installing-fluentd)

[Modifying the Config File](#modifying-the-config-file)

[Using fluent-logger-node](#using-fluent-logger-node)

-   [Obtaining the Most Recent
    Version](#obtaining-the-most-recent-version)
-   [A Sample Application](#a-sample-application)

[Production Deployments](#production-deployments)

-   [Output Plugins](#output-plugins)
-   [High-Availability Configurations of
    Fluentd](#high-availability-configurations-of-fluentd)
-   [Monitoring](#monitoring)
:::

Prerequisites
-------------

-   Basic knowledge of Node.js and NPM
-   Basic knowledge of Fluentd
-   Node.js 0.6 or higher

[]{#installing-fluentd}

Installing Fluentd
------------------

Please refer to the following documents to install fluentd.

-   [Install Fluentd with rpm Package](install-by-rpm)
-   [Install Fluentd with deb Package](install-by-deb)
-   [Install Fluentd with Ruby Gem](install-by-gem)
-   [Install Fluentd from source](install-from-source)

[]{#modifying-the-config-file}

Modifying the Config File
-------------------------

Next, please configure Fluentd to use the [forward Input
plugin](in_forward) as its data source.

``` {.CodeRay}
<source>
  @type forward
  port 24224
</source>
<match fluentd.test.**>
  @type stdout
</match>
```

Please restart your agent once these lines are in place.

``` {.CodeRay}
# for rpm/deb only
$ sudo /etc/init.d/td-agent restart
```

[]{#using-fluent-logger-node}

Using fluent-logger-node
------------------------

[]{#obtaining-the-most-recent-version}

### Obtaining the Most Recent Version

The most recent version of fluent-logger-node can be found
[here](https://www.npmjs.com/package/fluent-logger).

[]{#a-sample-application}

### A Sample Application

A sample [Express](http://expressjs.com/) app using fluent-logger-node
is shown below.

#### package.json

``` {.CodeRay}
{
  "name": "node-example",
  "version": "0.0.1",
  "dependencies": {
    "express": "^4.15.3",
    "fluent-logger": "^2.4.0"
  }
}
```

Now use *npm* to install your dependencies locally:

``` {.CodeRay}
$ npm install
```

#### index.js

This is the simplest web app.

``` {.CodeRay}
var express = require('express');
var logger = require('fluent-logger');
var app = express();

// The 2nd argument can be omitted. Here is a default value for options.
logger.configure('fluentd.test', {
  host: 'localhost',
  port: 24224,
  timeout: 3.0,
  reconnectInterval: 600000 // 10 minutes
});

app.get('/', function(request, response) {
  logger.emit('follow', {from: 'userA', to: 'userB'});
  response.send('Hello World!');
});
var port = process.env.PORT || 3000;
app.listen(port, function() {
  console.log("Listening on " + port);
});
```

Run the app and go to `http://localhost:3000/` in your browser. This
will send the logs to Fluentd.

``` {.CodeRay}
$ node index.js
```

The logs should be output to `/var/log/td-agent/td-agent.log` or stdout
of the Fluentd process via the [stdout Output plugin](out_stdout).

[]{#production-deployments}

Production Deployments
----------------------

[]{#output-plugins}

### Output Plugins

Various [output plugins](output-plugin-overview) are available for
writing records to other destinations:

-   Examples
    -   [Store Apache Logs into Amazon S3](apache-to-s3)
    -   [Store Apache Logs into MongoDB](apache-to-mongodb)
    -   [Data Collection into HDFS](http-to-hdfs)
-   List of Plugin References
    -   [Output to Another Fluentd](out_forward)
    -   [Output to MongoDB](out_mongo) or [MongoDB
        ReplicaSet](out_mongo_replset)
    -   [Output to Hadoop](out_webhdfs)
    -   [Output to File](out_file)
    -   [etc...](http://fluentd.org/plugin/)

[]{#high-availability-configurations-of-fluentd}

### High-Availability Configurations of Fluentd

For high-traffic websites (more than 5 application nodes), we recommend
using a high availability configuration of td-agent. This will improve
data transfer reliability and query performance.

-   [High-Availability Configurations of Fluentd](high-availability)

[]{#monitoring}

### Monitoring

Monitoring Fluentd itself is also important. The article below describes
general monitoring methods for td-agent.

-   [Monitoring Fluentd](monitoring)

::: {style="text-align:right"}
Last updated: 2017-01-30 13:27:02 UTC
:::

------------------------------------------------------------------------

::: {style="text-align:right"}
Versions \| [v1.0 (td-agent3)](/v1.0/articles/nodejs) \| ***v0.12*
(td-agent2) **
:::

------------------------------------------------------------------------

If this article is incorrect or outdated, or omits critical information,
please [let us
know](https://github.com/fluent/fluentd-docs/issues?state=open).
[Fluentd](http://www.fluentd.org/) is a open source project under [Cloud
Native Computing Foundation (CNCF)](https://cncf.io/). All components
are available under the Apache 2 License.
:::
:::
:::