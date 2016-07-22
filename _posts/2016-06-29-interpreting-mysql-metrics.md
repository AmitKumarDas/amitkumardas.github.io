---
layout: post
title: Interpreting MySQL metrics - WIP
---

Interpreting MySQL metrics **WIP**

> This article tries to itemize the various metrics w.r.t MySQL application.
> These help us in understanding the health of MySQL application and act thereof.

Server's uptime:

```
- The more the better.
- Analysis will not be misleading if it has been used for long times.
```

Key buffer usage:

```
- MySQL allocates system RAM for key buffer only if needed.
- Watch out for max amount of key buffer ever used by MySQL at once.
- Above is also termed as "high water mark".
- Above is not same as current usage.
- If "high water mark" > 80% then it indicates increase of key_buffer_size.
```

Write hits === Key writes

```
- Indexes i.e. keys are RAM based.
- This is the ratio of key writes to hard disk to key writes to RAM.
- It will be close to 0% during updates & inserts.
- It will be close to 90% during selects.
-
```


References:

- [Hack MySQL's mysqlreport](http://hackmysql.com/mysqlreport)
- [Monitor MySQL via a bash script](http://www.itchyninja.com/mysql-health-check-script/)
