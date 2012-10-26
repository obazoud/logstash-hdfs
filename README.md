Logstash HDFS plugin
=============

An HDFS plugin for [Logstash](http://logstash.net). This plugin is provided as an external plugin (see Usage below) and is not part of the Logstash project.

Usage
=============

run logstash with the `--pluginpath` (`-p`) command line argument to let logstash know where the plugin is. Also, you need to let Java know where your Hadoop JARs are, so set the `CLASSPATH` variable correctly.
E.G.

    CLASSPATH=$(find /path/to/hadoop -name '*.jar' | tr '\n' ':'):/etc/hadoop/conf java -jar logstash-1.1.3-monolithic.jar agent -f conf/hdfs-output.conf -p /path/to/cloned/logstash-hdfs


Config options are basically the same as the file output, but have a look at the `doc/` directory for specfics. 

HDFS Append and rewriting files
=============
Please note, HDFS versions prior to 2.x do not properly support append. See [HADOOP-8230](https://issues.apache.org/jira/browse/HADOOP-8230) for reference.
To enable append on HDFS, set _dfs.support.append_ in <tt>hdfs-site.conf</tt> (2.x) or _dfs.support.broken.append_ on 1.x, and use the *enable_append* config option:

    input {
        hdfs {
            path => "/path/to/output_file.log"
            enable_append => true
        }
    }

If append is not supported and the file already exists, the plugin will cowardly refuse to reopen the file for writing unless *enable_reopen* is set to true.
This is probably a very bad idea, you have been warned!

HFDS Flush
=============
Flush and sync don't actually work as promised on HDFS (see [HDFS-536](https://issues.apache.org/jira/browse/HDFS-536)).
In Hadoop 2.x, `hflush` provides flush-like functionality and the plugin will use `hflush` if it is available.
Nevertheless, flushing code has been left in the plugin in case `flush` and `sync` will work on some HDFS implementation.

License
=============
The plugin is released under the LGPL v3.

