# Alert

The unable to fetch oldest active transactions.

# Overview

This alert is fired when the log files contain a message like the following

```
2021-03-24 03:34:27,833+0000 ERROR [splicedb-hregion-0] [clustername-62b1659bfd] [hbase] [hbase-transactions-watcher-0] [hbase.TransactionsWatcher]: Unable to fetch oldestActiveTransaction. Leaving lowWatermarkTransaction untouched: 7508730880. Error cause by: java.util.concurrent.ExecutionException: com.google.protobuf.ServiceException: Error calling method SpliceRSRpcServices.getOldestActiveTransaction
```

# Steps

* Contact the database team
