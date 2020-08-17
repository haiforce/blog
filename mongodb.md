# MongoDB Tips

## Install and start

Refer to MongoDB documents online.

http://zetcode.com/javascript/mongodb/

## Trbouleshoot the performance

### check the mongd version

`$ mongod -v`

Sample result
```
2020-08-09T11:39:03.958+0800 I CONTROL  [initandlisten] MongoDB starting : pid=3172 port=27017 dbpath=/data/db 64-bit host=VM-0-2-ubuntu
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] db version v3.4.16
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] git version: 0d6a9242c11b99ddadcfb6e86a850b6ba487530a
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.0.2g  1 Mar 2016
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] allocator: tcmalloc
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] modules: none
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten] build environment:
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten]     distmod: ubuntu1604
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten]     distarch: x86_64
2020-08-09T11:39:03.962+0800 I CONTROL  [initandlisten]     target_arch: x86_64
2020-08-09T11:39:03.963+0800 I CONTROL  [initandlisten] options: { systemLog: { verbosity: 1 } }
2020-08-09T11:39:03.963+0800 D -        [initandlisten] User Assertion: 29:Data directory /data/db not found. src/mongo/db/service_context_d.cpp 98
2020-08-09T11:39:03.963+0800 I STORAGE  [initandlisten] exception in initAndListen: 29 Data directory /data/db not found., terminating
2020-08-09T11:39:03.963+0800 I NETWORK  [initandlisten] shutdown: going to close listening sockets...
2020-08-09T11:39:03.963+0800 I NETWORK  [initandlisten] shutdown: going to flush diaglog...
2020-08-09T11:39:03.963+0800 I CONTROL  [initandlisten] now exiting
2020-08-09T11:39:03.963+0800 I CONTROL  [initandlisten] shutting down with code:100
```
### Start console
`mongo --host 127.0.0.1:27017`

Solution #1: db.currentOp()

The db.currentOp() function lists the currently running queries with very detailed information. It also includes the duration they have been running (secs_running).
You can go to MongoDB console, run this function and analise the output, but I recommend to start with a filter (secs_running) to reduce the output, e.g:
db.currentOp({“secs_running”: {$gte: 3}}) 

> db.currentOp()

some sample output

```
{
			"desc" : "conn166",
			"threadId" : "139756922894080",
			"connectionId" : 166,
			"client" : "120.24.211.211:36224",
			"active" : true,
			"opid" : 76633,
			"secs_running" : 5,                                   <== here you identify the issue, 5 sec, oh my god
			"microsecs_running" : NumberLong(5952117),
			"op" : "update",
			"ns" : "government.thingsboardFaultCollection",       <== here you identify the collections
			"query" : {
				"name" : "31a024c1-4694-3864-932a-f02fe116d8a4"
			},
			"planSummary" : "COLLSCAN",
			"numYields" : 9803,
			"locks" : {
				"Global" : "w",
				"Database" : "w",
				"Collection" : "w"
			},
			"waitingForLock" : false,
			"lockStats" : {
				"Global" : {
					"acquireCount" : {
						"r" : NumberLong(9804),
						"w" : NumberLong(9804)
					}
				},
				"Database" : {
					"acquireCount" : {
						"w" : NumberLong(9804)
					}
				},
				"Collection" : {
					"acquireCount" : {
						"w" : NumberLong(9804)
					}
				}
			}
		},
```
