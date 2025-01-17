# Advanced Configuration for EFK -  Lab

## 1. What is the primary role of Fluentd in the EFK stack?
automated rollouts and rollbacks
Data storage and indexing
Data visualization
Log aggregation and data collection

## 2. Which Elasticsearch feature allows you to automate the creation of new indices when current ones meet specific condition such as size or age?

Index rollover
Sharding
Index replicas
Index templates

## 3. Which plugin type can be used in Fluentd to modify the data structure of a log message?

Filter plugins
Output plugins
Parser plugins
Input plugins

## 4. What is the main benefit of utilizing custom Fluentd plugins?

Restricting the flow of logs to specific destinations
Decrease in overall system performance
Enhancing log data with additional context
Increasing storage requirements



## 5. Which Elasticsearch index setting is critical for improving write performance in a log-heavy environment?

Decreasing the number of primary shards
Changing the refresh interval
Increasing the index buffer size
Increasing the number of replicas

## 6. For high availability in Elasticsearch, what is the recommended minimum number of master-eligible nodes that should be deployed within a cluster?

1
3
7
5
Explanation: This is because Elasticsearch uses a consensus-based approach for electing a master node, and having at least three master-eligible nodes helps ensure that the cluster can continue to operate even if one node fails.


## 7. In the context of Fluentd, what is the purpose of a buffer plugin?

To store log data temporarily before output
To compress log data for storage
To visualize log data
To parse log data

Explanation: In the context of Fluentd, a buffer plugin is used to temporarily store log data before it is processed and forwarded to its final destination.
This prevents loss of logs due to target unavailability, or network issues.





## 8. We have just discussed the importance of custom Fluentd plugins for log enrichment. Now, create a custom Fluentd filter plugin named my_custom_filter that adds a field called additional_data with the value static_info to all processed logs.
Place this file in the /root/fluentd/plugins folder. Create the directory not present in the above path, if any.
You just need to create the filter file for this task.
Note that fluentd accepts the files starting with prefix filter_ as plugins, so name the file accordingly.

Remember to follow the Ruby plugin development guide for Fluentd.
Access the Kibana UI from the top right of this page. Create an index pattern and analyze the logs.


This documentation can help:
https://docs.fluentd.org/plugin-development/api-plugin-filter

***
Solution:
require 'fluent/plugin/filter'

class Fluent::Plugin::MyCustomFilter < Fluent::Plugin::Filter
  Fluent::Plugin.register_filter('my_custom_filter', self)

  config_param :additional_data, :string, default: 'static_info'

  def filter(tag, time, record)
    record['additional_data'] = @additional_data
    record
  end
end


## 9. Having already created the custom plugin file, let's now add it to our fluentd configuration file in the /root/fluentd/etc folder.
Use the filter tag to add a new section to the config file including the custom plugin.
Ensure that this section is added before the match section.

Refer to the Description page to view how to add the plugin in the configuration file.


Solution: To the fluent.conf file inside the /root/fluentd/etc/ directory, add the following before the match section:

<filter **>
  @type my_custom_filter
  additional_data static_info
</filter>


