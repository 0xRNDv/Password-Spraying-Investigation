# Splunk Queries

## Failed Authentication

```spl
index="t1110-003" event.code=4625
| dedup winlog.event_data.TargetUserName
| table _time winlog.event_data.TargetUserName winlog.event_data.IpAddress winlog.event_data.LogonType
```

## Password Spraying Pattern

```spl
index="t1110-003" event.code=4625 winlog.event_data.IpAddress="192.168.1.60"
| stats count as FailedAttempts by winlog.event_data.TargetUserName winlog.event_data.IpAddress
| sort -FailedAttempts
```

## Successful Authentication

```spl
index="t1110-003" event.code=4624 winlog.event_data.IpAddress="192.168.1.60"
| table _time winlog.event_data.TargetUserName winlog.event_data.IpAddress winlog.event_data.LogonType
```

## the Targeted Protocol

```spl
index="t1110-003" event.code=261
```

## the Attack Timeline

```spl
index="t1110-003"
winlog.event_data.IpAddress="192.168.1.60"
(event.code=4625 OR event.code=4624)
| sort 0 _time
| streamstats count
| eventstats max(count) as total
| where count<=5 OR count>=total-4
| table _time event.code winlog.event_data.TargetUserName winlog.event_data.LogonType
```