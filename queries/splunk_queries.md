# Splunk Detection Queries

## Verify Sysmon Logs

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
```

---

## Verify Log Source

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| stats count by source
```

---

## PowerShell Activity Detection (T1059.001)

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" powershell
```

Most Helpful Sysmon Event ID:

* Event ID 1 (Process Creation)

---

## Scheduled Task Detection (T1053.005)

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" schtasks
```

Alternative:

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
"LabHiddenTask"
```

Most Helpful Sysmon Event ID:

* Event ID 1 (Process Creation)

---

## MSHTA Execution Detection (T1218.005)

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" mshta
```

Most Helpful Sysmon Event ID:

* Event ID 1 (Process Creation)

---

## LSASS Dump Detection (T1003.001)

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" procdump
```

Alternative:

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" lsass
```

Most Helpful Sysmon Event ID:

* Event ID 10 (Process Access)

---

## Registry Modification Detection (T1112)

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventID=13
```

Most Helpful Sysmon Event ID:

* Event ID 13 (Registry Value Set)

---

## Network Connection Events

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventID=3
```

---

## Process Creation Events

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventID=1
```

---

## Sysmon Event Distribution

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| rex "<EventID>(?<EventID>\d+)</EventID>"
| stats count by EventID
| sort -count
```
