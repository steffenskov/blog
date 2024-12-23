---
id: 216
title: 'Finding unused indexes on MS SQL server'
date: '2013-07-03T15:14:32+01:00'
author: Steffen
excerpt: "Do you ever find yourself not getting rid of old indexes, when they're no longer needed ?\r\n\r\nI guess we all do sometimes, but luckily SQL server keeps track of the usage of indexes, which allows you to find those unused indexes easily."
layout: post
guid: 'http://www.ckode.dk/?p=216'
permalink: /programming/finding-unused-indexes-on-ms-sql-server/
categories:
    - Programming
tags:
    - SQL
---

Do you ever find yourself not getting rid of old indexes, when they’re no longer needed ?

I guess we all do sometimes, but luckily SQL server keeps track of the usage of indexes, which allows you to find those unused indexes easily.

The SQL below will find all indexes, how many times they’ve been written to and read from, as well as when the index was last read from.

The interesting part is obviously the last read, as well as number of reads – if the number of reads is 0, or the last read was 2 months ago, there’s a huge chance the index isn’t used anymore and could thus be dropped.

Do note that the SQL below will only work on MS SQL server 2008 or newer, due to the way it figures out “LastRead”.

```

DECLARE @dbid int = db_id()

;With filteredData as(
    SELECT 
		object_name(s.object_id) as [Table], 
		i.name as [Index],
		s.system_updates+ s.user_updates as Writes, 
		s.user_seeks + s.user_scans + s.user_lookups + s.system_lookups + s.system_scans + s.system_seeks as Reads,

		(SELECT max(v) FROM (Values
		(s.last_system_lookup),
		(s.last_system_scan),
		(s.last_system_seek),
		(s.last_user_lookup),
		(s.last_user_scan),
		(s.last_user_seek)) as value(v)) as LastRead

    FROM sys.dm_db_index_usage_stats AS s 
    INNER JOIN sys.indexes AS i
    ON s.object_id = i.object_id
    AND i.index_id = s.index_id
	AND i.is_primary_key = 0
	AND i.is_unique_constraint = 0
    WHERE objectproperty(s.object_id,'IsUserTable') = 1
    AND s.database_id = @dbid)
    
SELECT *
FROM filteredData 
ORDER BY LastRead ASC
```