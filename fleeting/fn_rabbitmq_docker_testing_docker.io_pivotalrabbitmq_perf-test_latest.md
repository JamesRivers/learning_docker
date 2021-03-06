 - fn_rabbitmq_docker_testing_docker.io_pivotalrabbitmq_perf-test_latest
	- Imagine Communications - MyImagine Academy Content
	- Created on the: 2021-09-09 10:34
	-  <img src="https://avatars.githubusercontent.com/u/8113173?s=60&v=4" width="25" height="25" align="left">  James Rivers
	- Written against (version): 
	- Sources: 
		- https://www.rabbitmq.com/uri-spec.html
		- https://github.com/rabbitmq/rabbitmq-perf-test
	- Author Notes: 
	- Tags: 
	- ICON set : Warning ⚠️ / Notes 🗒 / Version 🌱 / Knowledge 🧠 / WWW 🕸 / Learning AIM 🎯
---
# rabbitmq test tool 
`docker pull pivotalrabbitmq/perf-test`

--help
```bash
 -?,--help                                   show usage
 -a,--autoack                                auto ack
 -A,--multi-ack-every <arg>                  multi ack every
 -ad,--auto-delete <arg>                     should the queue be
                                             auto-deleted, default is true
 -b,--heartbeat <arg>                        heartbeat interval
 -B,--body <arg>                             comma-separated list of files
                                             to use in message bodies
 -bc,--body-count <arg>                      number of pre-generated
                                             message bodies. Use with
                                             --json-body. Default is 100.
 -bfc,--body-field-count <arg>               number of pre-generated
                                             fields and values for body.
                                             Use with --json-body. Default
                                             is 1000.
 -c,--confirm <arg>                          max unconfirmed publishes
 -C,--pmessages <arg>                        producer message count
 -ca,--consumer-args <arg>                   consumer arguments as
                                             key/values pairs, separated
                                             by commas, e.g. x-priority=10
 -cri,--connection-recovery-interval <arg>   connection recovery interval
                                             in seconds. Default is 5
                                             seconds. Interval syntax,
                                             e.g. 30-60, is supported to
                                             specify an random interval
                                             between 2 values between each
                                             attempt.
 -ct,--confirm-timeout <arg>                 waiting timeout for
                                             unconfirmed publishes before
                                             failing (in seconds)
 -ctp,--consumers-thread-pools <arg>         number of thread pools to use
                                             for all consumers, default is
                                             to use a thread pool for each
                                             consumer
 -d,--id <arg>                               test ID
 -D,--cmessages <arg>                        consumer message count
 -dcr,--disable-connection-recovery          disable automatic connection
                                             recovery
 -e,--exchange <arg>                         exchange name
 -E,--exclusive                              use server-named exclusive
                                             queues. Such queues can only
                                             be used by their declaring
                                             connection!
 -env,--environment-variables                show usage with environment
                                             variables
 -f,--flag <arg>                             message flag(s), supported
                                             values: persistent and
                                             mandatory. Use the option
                                             several times to specify
                                             several values.
 -h,--uri <arg>                              connection URI
 -H,--uris <arg>                             connection URIs (separated by
                                             commas)
 -hst,--heartbeat-sender-threads <arg>       number of threads for
                                             producers and consumers
                                             heartbeat senders
 -i,--interval <arg>                         sampling interval in seconds
 -jb,--json-body                             generate a random JSON
                                             document for message body.
                                             Use with --size.
 -k,--routing-key <arg>                      routing key
 -K,--random-routing-key                     use random routing key per
                                             message
 -l,--legacy-metrics                         display legacy metrics
                                             (min/avg/max latency)
 -L,--consumer-latency <arg>                 consumer latency in
                                             microseconds
 -m,--ptxsize <arg>                          producer tx size
 -M,--framemax <arg>                         frame max
 -mh,--metrics-help                          show metrics usage
 -mp,--message-properties <arg>              message properties as
                                             key/value pairs, separated by
                                             commas, e.g. priority=5
 -ms,--use-millis                            should latency be collected
                                             in milliseconds, default is
                                             false. Set to true if
                                             producers are consumers run
                                             on different machines.
 -n,--ctxsize <arg>                          consumer tx size
 -na,--nack                                  nack messages, requeue them
                                             by default.
 -niot,--nio-threads <arg>                   number of NIO threads to use
 -niotp,--nio-thread-pool <arg>              size of NIO thread pool,
                                             should be slightly higher
                                             than number of NIO threads
 -o,--output-file <arg>                      output file for timing
                                             results
 -p,--predeclared                            allow use of predeclared
                                             objects
 -P,--publishing-interval <arg>              publishing interval in
                                             seconds (opposite of producer
                                             rate limit)
 -pi,--polling-interval <arg>                time to wait before polling
                                             with basic.get, in
                                             millisecond, default is 0.
 -po,--polling                               use basic.get to consume
                                             messages. Do not use this in
                                             real applications.
 -prsd,--producer-random-start-delay <arg>   max random delay in seconds
                                             to start producers
 -pst,--producer-scheduler-threads <arg>     number of threads to use when
                                             using --publishing-interval
 -q,--qos <arg>                              consumer prefetch count
 -Q,--global-qos <arg>                       channel prefetch count
 -qa,--queue-args <arg>                      queue arguments as key/value
                                             pairs, separated by commas,
                                             e.g. x-max-length=10
 -qf,--queue-file <arg>                      file to look up queue names
                                             from
 -qp,--queue-pattern <arg>                   queue name pattern for
                                             creating queues in sequence
 -qpf,--queue-pattern-from <arg>             queue name pattern range
                                             start (inclusive)
 -qpt,--queue-pattern-to <arg>               queue name pattern range end
                                             (inclusive)
 -qq,--quorum-queue                          create quorum queue(s)
 -r,--rate <arg>                             producer rate limit
 -R,--consumer-rate <arg>                    consumer rate limit
 -re,--requeue <arg>                         should nacked messages be
                                             requeued, default is true.
 -rkcs,--routing-key-cache-size <arg>        size of the random routing
                                             keys cache. See
                                             --random-routing-key.
 -S,--slow-start                             start consumers slowly (1 sec
                                             delay between each)
 -s,--size <arg>                             message size in bytes
 -sb,--skip-binding-queues                   don't bind queues to the
                                             exchange
 -se,--sasl-external                         use SASL EXTERNAL
                                             authentication, default is
                                             false. Set to true if using
                                             client certificate
                                             authentication with the
                                             rabbitmq_auth_mechanism_ssl
                                             plugin.
 -sni,--server-name-indication <arg>         server names for Server Name
                                             Indication TLS parameter,
                                             separated by commas
 -sst,--servers-startup-timeout <arg>        start timeout in seconds (in
                                             case the servers(s) is (are)
                                             not available when the run
                                             starts). Default is to fail
                                             immediately if the servers(s)
                                             is (are) not available.
 -st,--shutdown-timeout <arg>                shutdown timeout, default is
                                             5 seconds
 -sul,--servers-up-limit <arg>               number of available servers
                                             needed before starting the
                                             run. Used in conjunction with
                                             --servers-start-timeout.
                                             Default is deduced from --uri
                                             or --uris.
 -t,--type <arg>                             exchange type
 -T,--body-content-type <arg>                body content-type
 -u,--queue <arg>                            queue name
 -udsc,--use-default-ssl-context             use JVM default SSL context
 -v,--version                                print version information
 -vl,--variable-latency <arg>                variable consumer processing
                                             latency with
                                             [MICROSECONDS]:[DURATION]
                                             syntax, where [MICROSECONDS]
                                             integer >= 0 and [DURATION]
                                             integer > 0. Use the option
                                             several times to specify
                                             several values.
 -vr,--variable-rate <arg>                   variable publishing rate with
                                             [RATE]:[DURATION] syntax,
                                             where [RATE] integer >= 0 and
                                             [DURATION] integer > 0. Use
                                             the option several times to
                                             specify several values.
 -vs,--variable-size <arg>                   variable message size with
                                             [SIZE]:[DURATION] syntax,
                                             where [SIZE] integer > 0 and
                                             [DURATION] integer > 0. Use
                                             the option several times to
                                             specify several values.
 -x,--producers <arg>                        producer count
 -X,--producer-channel-count <arg>           channels per producer
 -y,--consumers <arg>                        consumer count
 -Y,--consumer-channel-count <arg>           channels per consumer
 -z,--time <arg>                             run duration in seconds
                                             (unlimited by default)
```

```
docker run -it --rm --network wyc-dev-network pivotalrabbitmq/perf-test:latest --uri amqp://admin:ImagineDB1@172.16.0.104:5672/Versio
```

`amqp://user:pass@host:10000/vhost`

Results 
![](/attachments/Pasted%20image%2020210909104402.png)
