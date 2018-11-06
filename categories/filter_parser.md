::: {#main .section}
::: {#page}
::: {.topic_content}
::: {style="text-align:right"}
::: {style="text-align:right"}
Versions \| [v1.0 (td-agent3)](/v1.0/articles/filter_parser) \|
***v0.12* (td-agent2) **
:::
:::

------------------------------------------------------------------------

parser Filter Plugin
====================

The `filter_parser` filter plugin "parses" string field in event records
and mutates its event record with parsed result.

[]{#example-configurations}

::: {#table-of-contents .section}
### Table of Contents

[Example Configurations](#example-configurations)

[Parameters](#parameters)

-   [format](#format)
-   [key\_name](#key_name)
-   [reserve\_data](#reserve_data)
-   [suppress\_parse\_error\_log](#suppress_parse_error_log)
-   [ignore\_key\_not\_exist](#ignore_key_not_exist)
-   [replace\_invalid\_sequence](#replace_invalid_sequence)
-   [inject\_key\_prefix](#inject_key_prefix)
-   [hash\_value\_field](#hash_value_field)
-   [time\_parse](#time_parse)
-   [emit\_invalid\_record\_to\_error](#emit_invalid_record_to_error)

[Learn More](#learn-more)
:::

Example Configurations
----------------------

`filter_parser` is included in Fluentd's core since v0.12.29. No
installation required. If you want to use `filter_parser` with lower
fluentd versions, need to install `fluent-plugin-parser`.

`filter_parser` has just same with `in_tail` about `format` and
`time_format`:

``` {.CodeRay}
<filter foo.bar>
  @type parser
  format /^(?<host>[^ ]*) [^ ]* (?<user>[^ ]*) \[(?<time>[^\]]*)\] "(?<method>\S+)(?: +(?<path>[^ ]*) +\S*)?" (?<code>[^ ]*) (?<size>[^ ]*)$/
  time_format %d/%b/%Y:%H:%M:%S %z
  key_name message
</filter>
```

`filter_parser` uses built-in parser plugins and your own customized
parser plugin, so you can re-use pre-defined format like `apache`,
`json` and etc. See document page for more details: [Parser Plugin
Overview](/articles/parser-plugin-overview)

[]{#parameters}

Parameters
----------

[]{#format}

### format

This is required parameter. Specify parser format or regexp pattern.

[]{#key_name}

### key\_name

This is required parameter. Specify field name in the record to parse.

[]{#reserve_data}

### reserve\_data

Keep original key-value pair in parsed result. Default is `false`.

``` {.CodeRay}
<filter foo.bar>
  @type parser
  format json
  key_name log
  reserve_data true
</filter>
```

With above configuration, result is below:

``` {.CodeRay}
# input data:  {"key":"value","log":"{\"user\":1,\"num\":2}"}
# output data: {"key":"value","log":"{\"user\":1,\"num\":2}","user":1,"num":2}
```

Without `reserve_data`, result is below

``` {.CodeRay}
# input data:  {"key":"value","log":"{\"user\":1,\"num\":2}"}
# output data: {"user":1,"num":2}
```

[]{#suppress_parse_error_log}

### suppress\_parse\_error\_log

If `true`, a plugin suppresses `pattern not match` warning log. Default
is `false`.

This parameter is useful for parsing mixed logs and you want to ignore
non target lines.

[]{#ignore_key_not_exist}

### ignore\_key\_not\_exist

Ignore "key not exist" log. Default is `false`.

Useful case is same with `suppress_parse_error_log`.

[]{#replace_invalid_sequence}

### replace\_invalid\_sequence

If `true`, invalid string is replaced with safe characters and re-parse
it. Default is `false`.

[]{#inject_key_prefix}

### inject\_key\_prefix

Store parsed values with specified key name prefix. Default is `nil`.

``` {.CodeRay}
<filter foo.bar>
  @type parser
  format json
  key_name log
  reserve_data true
  inject_key_prefix data.
</filter>
```

With above configuration, result is below:

``` {.CodeRay}
# input data:  {"log": "{\"user\":1,\"num\":2}"}
# output data: {"log":"{\"user\":1,\"num\":2}","data.user":1, "data.num":2}
```

[]{#hash_value_field}

### hash\_value\_field

Store parsed values as a hash value in a field. Default is `nil`.

``` {.CodeRay}
<filter foo.bar>
  @type parser
  format json
  key_name log
  hash_value_field parsed
</filter>
```

With above configuration, result is below:

``` {.CodeRay}
# input data:  {"log": "{\"user\":1,\"num\":2}"}
# output data: {"parsed":{"user":1,"num":2}}
```

[]{#time_parse}

### time\_parse

If false, time parsing is disabled in the parser. Default is true.

[]{#emit_invalid_record_to_error}

### emit\_invalid\_record\_to\_error

Emit invalid record to `@ERROR` label. Default is `false`. Invalid cases
are

-   key not exist
-   format is not matched
-   unexpected error

You can rescue unexpected format logs in `@ERROR` label.

[]{#learn-more}

Learn More
----------

-   [Filter Plugin Overview](filter-plugin-overview)

::: {style="text-align:right"}
Last updated: 2017-03-16 10:36:00 UTC
:::

------------------------------------------------------------------------

::: {style="text-align:right"}
Versions \| [v1.0 (td-agent3)](/v1.0/articles/filter_parser) \|
***v0.12* (td-agent2) **
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