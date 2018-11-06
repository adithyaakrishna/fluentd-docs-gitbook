::: {#main .section}
::: {#page}
::: {.topic_content}
::: {style="text-align:right"}
::: {style="text-align:right"}
Versions \| ***v0.12* (td-agent2) **
:::
:::

------------------------------------------------------------------------

GeoIP Output Plugin
===================

The `out_geoip` Buffered Output plugin adds geographic location
information to logs using the Maxmind GeoIP databases.
:::
:::
:::

This document doesn\'t describe all parameters. If you want to know full
features, check the Further Reading section.

[]{#prerequisites}

::: {#table-of-contents .section}
### Table of Contents

[Prerequisites](#prerequisites)

[Install](#install)

[Example Configuration](#example-configuration)

[Parameters](#parameters)

-   [geoip\_lookup\_key (required)](#geoip_lookup_key-(required))
-   [remove\_tag\_prefix / add\_tag\_prefix (requires one or the
    other)](#remove_tag_prefix-/-add_tag_prefix-(requires-one-or-the-other))
-   [enable\_key\_\*\*\* (requires at least
    one)](#enable_key_***-(requires-at-least-one))
-   [include\_tag\_key](#include_tag_key)
-   [tag\_key](#tag_key)

[Buffer Parameters](#buffer-parameters)

-   [buffer\_type](#buffer_type)
-   [buffer\_queue\_limit,
    buffer\_chunk\_limit](#buffer_queue_limit,-buffer_chunk_limit)
-   [flush\_interval](#flush_interval)

[Use Cases](#use-cases)

[Further Reading](#further-reading)
:::

Prerequisites
-------------

-   The GeoIP library.

    :::term \# for RHEL/CentOS \$ sudo yum install geoip-devel
    --enablerepo=epel

    \# for Ubuntu/Debian \$ sudo apt-get install libgeoip-dev

    \# for MacOSX (brew) \$ brew install geoip

[]{#install}

Install
-------

`out_geoip` is not included in td-agent. All users must install the
fluent-plugin-geoip gem using the following command.

``` {.CodeRay}
$ fluent-gem install fluent-plugin-geoip
$ sudo /usr/sbin/td-agent-gem install fluent-plugin-geoip
```

[]{#example-configuration}

Example Configuration
---------------------

The configuration shown below adds geolocation information to
apache.access

``` {.CodeRay}
<match test.message>
  @type geoip
  geoip_lookup_key        host
  enable_key_country_code geoip_country
  enable_key_city         geoip_city
  enable_key_latitude     geoip_lat
  enable_key_longitude    geoip_lon
  remove_tag_prefix       test.
  add_tag_prefix          geoip.
  flush_interval          5s
</match>


:::text
# original record
test.message {
  "host":"66.102.9.80",
  "message":"test"
}

# output record
geoip.message: {
  "host":"66.102.9.80",
  "message":"test",
  "geoip_country":"US",
  "geoip_city":"Mountain View",
  "geoip_lat":37.4192008972168,
  "geoip_lon":-122.05740356445312
}
```

Please see the [fluent-plugin-geoip
README](https://github.com/y-ken/fluent-plugin-geoip#readme) for further
details.

[]{#parameters}

Parameters
----------

[]{#geoip_lookup_key-(required)}

### geoip\_lookup\_key (required)

Specifies the geoip lookup field (default: host) If accessing a nested
hash value, delimit the key with '.', as in 'host.ip'.

[]{#remove_tag_prefix-/-add_tag_prefix-(requires-one-or-the-other)}

### remove\_tag\_prefix / add\_tag\_prefix (requires one or the other)

Set tag replace rule.

[]{#enable_key_***-(requires-at-least-one)}

### enable\_key\_\*\*\* (requires at least one)

Specifies the geographic data that will be added to the record. The
supported parameters are shown below:

-   enable\_key\_city
-   enable\_key\_latitude
-   enable\_key\_longitude
-   enable\_key\_country\_code3
-   enable\_key\_country\_code
-   enable\_key\_country\_name
-   enable\_key\_dma\_code
-   enable\_key\_area\_code
-   enable\_key\_region

[]{#include_tag_key}

### include\_tag\_key

Set to `true` to include the original tag name in the record. (default:
false)

[]{#tag_key}

### tag\_key

Adds the tag name into the record using this value as the key name When
`include_tag_key` is set to `true`.

[]{#buffer-parameters}

Buffer Parameters
-----------------

For advanced usage, you can tune Fluentd's internal buffering mechanism
with these parameters.

[]{#buffer_type}

### buffer\_type

The buffer type is `memory` by default ([buf\_memory](buf_memory)). The
`file` ([buf\_file](buf_file)) buffer type can be chosen as well. Unlike
many other output plugins, the `buffer_path` parameter MUST be specified
when using `buffer_type file`.

[]{#buffer_queue_limit,-buffer_chunk_limit}

### buffer\_queue\_limit, buffer\_chunk\_limit

The length of the chunk queue and the size of each chunk, respectively.
Please see the [Buffer Plugin Overview](buffer-plugin-overview) article
for the basic buffer structure. The default values are 64 and 256m,
respectively. The suffixes "k" (KB), "m" (MB), and "g" (GB) can be used
for buffer\_chunk\_limit.

[]{#flush_interval}

### flush\_interval

The interval between forced data flushes. The default is nil (don't
force flush and wait until the end of time slice + time\_slice\_wait).
The suffixes "s" (seconds), "m" (minutes), and "h" (hours) can be used.

#### log\_level option

The `log_level` option allows the user to set different levels of
logging for each plugin. The supported log levels are: `fatal`, `error`,
`warn`, `info`, `debug`, and `trace`.

Please see the [logging article](logging) for further details.

[]{#use-cases}

Use Cases
---------

#### Plot real time access statistics on a world map using Elasticsearch and Kibana

The `country_code` field is needed to visualize access statistics on a
world map using [Kibana](http://www.elasticsearch.org/overview/kibana/).

Note: The following plugins are required: \* fluent-plugin-geoip \*
fluent-plugin-elasticsearch

``` {.CodeRay}
<match td.apache.access>
  @type geoip

  # Set key name for the client ip address values
  geoip_lookup_key     host

  # Specify key name for the country_code values
  enable_key_country_code  geoip_country

  # Swap tag prefix from 'td.' to 'es.'
  remove_tag_prefix    td.
  add_tag_prefix       es.
</match>

<match es.apache.access>
  @type            elasticsearch
  host            localhost
  port            9200
  type_name       apache
  logstash_format true
  flush_interval  10s
</match>
```

[]{#further-reading}

Further Reading
---------------

-   [fluent-plugin-geoip
    repository](https://github.com/y-ken/fluent-plugin-geoip)

::: {style="text-align:right"}
Last updated: 2016-08-29 20:30:56 UTC
:::

------------------------------------------------------------------------

::: {style="text-align:right"}
Versions \| ***v0.12* (td-agent2) **
:::

------------------------------------------------------------------------

If this article is incorrect or outdated, or omits critical information,
please [let us
know](https://github.com/fluent/fluentd-docs/issues?state=open).
[Fluentd](http://www.fluentd.org/) is a open source project under [Cloud
Native Computing Foundation (CNCF)](https://cncf.io/). All components
are available under the Apache 2 License.