---
title: Handy SQL Queries
date: 2023-05-23
tags: 
- sql 
- cucm 
- cuc 
- uccx
---

Here's a growing list of handy SQL queries that I've found useful.

## CUCM

Gather phone information on a specific node:

```
show risdb query phone
```

Find a specific speed dial number. The device name, description, speed dial index, label, and number are listed:

```
run sql select d.name, d.description, sd.speeddialindex, sd.label, sd.speeddialnumber from speeddial as sd inner join device as d on sd.fkdevice=d.pkid where speeddialnumber like '655%'
```

View call forwarding status on all line apperances:

```
run sql select n.dnorpattern, cfd.cfadestination, cfd.cfavoicemailenabled from numplan as n inner join callforwarddynamic as cfd on cfd.fknumplan=n.pkid order by n.dnorpattern
```

Find a specific external number mask:

```
run sql select dnorpattern, d.description, e164mask from devicenumplanmap inner join numplan on fknumplan=numplan.pkid join device d on fkdevice = d.pkid where e164mask= "3335683130"
```

## CUC

List users with total, inbox, and deleted messages:

```
run cuc dbquery unitymbxdb1 select alias as UserID, count (*) as messages, sum(case when deleted='0' then 1 else 0 end) as Inboxmessages, sum(case when deleted='1' then 1 else 0 end) as Deletedmessages from vw_message, unitydirdb: vw_mailbox, unitydirdb:vw_user where mailboxobjectid in (select mailboxid from vw_mailbox where unitydirdb:vw_user.objectid = unitydirdb:vw_mailbox.userobjectid) group by alias order by messages desc
```

List messages for a specific mailbox:

```
run cuc dbquery unitymbxdb1 select alias as UserID, count (*) as messages, sum(case when deleted='0' then 1 else 0 end) as Inboxmessages, sum(case when deleted='1' then 1 else 0 end) as Deletedmessages from vw_message, unitydirdb: vw_mailbox, unitydirdb:vw_user where mailboxobjectid in (select mailboxid from vw_mailbox where unitydirdb:vw_user.objectid = unitydirdb:vw_mailbox.userobjectid) and alias like 'jsmith%' group by alias order by messages desc
```
## UCCX

Check skill mapping:

```
run uccx sql db_cra select s.skillname, rsm.competencelevel, r.resourceLoginID, r.extension, r.resourceFirstName, r.ResourceLastName,t.teamname from skill s inner join resourceskillmapping rsm on s.skillid = rsm.skillid inner join resource r on rsm.resourceskillmapid = r.resourceskillmapid join team t on r.assignedteamid = t.teamid where s.active = 't' and r.active = 't' order by s.skillname, competencelevel, resourceloginid
```
