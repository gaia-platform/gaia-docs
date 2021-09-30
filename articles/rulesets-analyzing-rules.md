---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---
# Analyzing your rules

## Rule Statistics

The rules engine logs statistics according to settings in the
\`gaia.conf\` configuration. The following options determining logging
behavior:

| Option                       | Default if Unspecified | Description    |
|------------------------------|------------------------|----------------|
| stats\_log\_interval         | 10 seconds             | The interval in seconds for how frequently the rules engine logs statistics. All stats (counts, averages, etc) are calculated anew for each interval. |
| log\_individual\_rule\_stats | False                  | If set to true, then in addition to rollup statistics for all rules, the same statistics are calculated for each Rule. |

Here is sample output when of the Rule\_stats log when individual Rule
statistics are not collected:

```
2020-11-30T15:15:37 30026 <rules_stats>;: 
------------------------- sched invoc pend aband retry excep avg lat max
lat avg exec max exec

2020-11-30T15:15:37 30026 <rules_stats>;: thread load:
0.49 % 21 21 0 0 0 0 15.26 ms 23.59 ms 1.46 ms 9.31 ms

2020-11-30T15:15:47 30026 <rules_stats>;: thread load:
0.65 % 30 30 0 0 0 0 13.66 ms 19.66 ms 0.28 ms 7.01 ms
```

For example, the first data row shows that 21 rules were scheduled and
21 rules were invoked.

If individual Rule statistics are turned on, sample output might look
like:

```
2020-11-30T15:13:25 28975 <rules_stats>:
------------------------- sched invoc pend aband retry excep avg lat max
lat avg exec max exec

2020-11-30T15:13:25 28975 <rules_stats>: thread load:
0.73 % 28 28 0 0 0 1 15.11 ms 24.91 ms 0.74 ms 10.61 ms

2020-11-30T15:13:25 28975 <rules_stats>;:
incubator_ruleset::1_sensor 27 27 0 0 0 1 15.25 ms 24.91 ms 0.77 ms
10.61 ms

2020-11-30T15:13:25 28975 <rules_stats>;:
incubator_ruleset::3 1 1 0 0 0 0 11.30 ms 11.30 ms 0.01 ms 0.01 ms
```

Here we see that of the 28 scheduled/invocations in this time interval, the rules engine invoked **incubator_ruleset::1_sensor**  27 times and **incubator_ruleset::3** once.

## Rule Tracing

To display the rules traces to the console, edit your *gaia\_log.conf* file and add the following entry:

```
logger
name = "rules"
sinks = "console",
"file_rotating"
level = "trace"
```

Sample output from on console will appear similar to the following:

```
2020-11-30T15:35:33-08:00 trace 30862 30878 <rules>:
call: incubator_ruleset::1_sensor

2020-11-30T15:35:33-08:00 trace 30862 30874 <rules>:
call: incubator_ruleset::1_sensor

2020-11-30T15:35:33-08:00 trace 30862 30878 <rules>:
return: incubator_ruleset::1_sensor

2020-11-30T15:35:33-08:00 trace 30862 30874 <rules>:
return: incubator_ruleset::1_sensor
```

Note that the first number following "trace" is the process id. The
second number is the thread id.

If an exception occurs, the tracing displays output similar to the
following:

```
2020-11-30T15:46:34-08:00 trace 31036 31068 <rules>:
exception: incubator_ruleset::2, The rules engine has not been
initialized yet.
```