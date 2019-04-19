Alert aggregation example in Icinga2

# Aggregations in icinga2

I was having trouble figuring out how to do basic service aggregation in icinga2. I found a few resources on the web, but nothing that matched what I really wanted to do. The assistance of those findings, I was able to get a couple of different examples working.

# Host aggregation

This example is pretty much straight out of the docs, but I wanted to recreate it as a starting point.

Three hosts were added to the [hosts.conf](hosts.conf) file for 3 separate apache servers. These are basic host monitors, nothing special. The `vars.apache_cluster = "1"` was added for a different test, so that can be ignored in the context of this aggregation example.

The aggregation work takes place in `object Host "apache-cluster-check-host"` which was added to the same file. This check using the dummy check_command, which lets us write our custom logic. The check gets the current stat of all of the nodes in the cluster (defined via `vars.cluster_nodes`). If two or more of the cluster member hosts are down, the aggregated host check is marked as down.

# Service aggregation

This example uses the check_cluster nagios plugin. It is added via the [commands.conf](commands.conf) file.
The `object CheckCommand "check_cluster"` section adds a new plugin to check the status of a cluster. Honestly, this plugin is not that useful. The example uses it, but next time I would skip this a just build the logic itself.

The `apply Service "apache-cluster-1"` section applies an http check to each of the hosts that has the variable `apache_cluster == "1"` as part of the host definition (that thing I told you to ignore above).

The `apply Service "apache-cluster-check-agg"` does the aggregation checking. Most of the work in this section is just setting up the call to the check_cluster plugin. A better implementation would probably be to just write logic similar to the host check.

The `object Host "apache-cluster-hosts"` section was added to the [hosts.conf](hosts.conf) file and is simply used as the host to which we attach the `apache-cluster-check-agg` service.


# References
The following items helped me out...
- [Icinga2 Advanced Topics](https://icinga.com/docs/icinga2/latest/doc/08-advanced-topics/)
- [Check cluster plugin](https://www.monitoring-plugins.org/doc/man/check_cluster.html)
- [check_cluster - How To?](https://github.com/monitoring-plugins/monitoring-plugins/issues/1453)
