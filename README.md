# spark-flame

"6:58 are you sure where my Spark is?"

This is _very_ rough way of generating flame graphs from Spark executors. But it's a starting point.

* `ansible-playbook -i inventory.ini spark-flame.playbook  --extra-vars 'pattern=[YARN_APP_ID]'`

* Mark your workers using [workers] in inventory.ini

* Only works currently on YARN

* Compile [perf-map-agent](https://github.com/jrudolph/perf-map-agent) and drop the attach-main.jar and libperfmap.so files into the 'files' directory. Also chuck in flamegraph.pl and stackcollapse-perf.pl from Brendan Gregg's [FlameGraph](https://github.com/brendangregg/FlameGraph) repo. We generate the flame graphs server-side at the moment.

* Make sure you pass ``--conf spark.executor.extraJavaOptions=-XX:+PreserveFr
amePointer`` into either the spark-shell or spark-submit jobs so the executors preserve their frame pointer

* You can pass different options using `perf-options` var in ansible. Currently it's `-ag -F 97' (perf on all CPUs, generate call graph and profile at 97Hz)

* Currently, if the playbook doesn't find a PID on a worker matching the YARN application id, it'll error out on that node. But it'll continue on the ones where the job *is* running, so why are you complaining? (I will fix this eventually)

* If there are multiple executors running on a node, it *should* collapse them all into a single graph, as the perf command is passed all the PIDs it finds.

* Yes, you will need `perf` installed.

* Tested with Spark 2.0 on Google Dataproc (which seems to be running Debian 8.5 with a 3.16 kernel. So no fancy BPF stuff going on here!)

* Yes, that shell command that does a su to yarn is a touch problematic.

* Any glaring problems are due to me being ill and not sleeping for 24 hours as opposed to anything else ;). (sub: please check)

