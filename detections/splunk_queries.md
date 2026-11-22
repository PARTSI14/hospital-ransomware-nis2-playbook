# Splunk queries (auto-generated from the Sigma rules)

Converted with pySigma + pysigma-backend-splunk (Windows pipeline). Add your own `index=` / `sourcetype=` prefix (for example Sysmon Event ID 1 or Windows Security 4688 with command-line logging enabled).

## Hospital Ransomware - Windows Event Log Cleared via Command Line

Source: `sigma/hosp_ransom_event_log_clearing.yml`

```spl
(Image="*\\wevtutil.exe" (CommandLine IN ("* cl *", "* clear-log *"))) OR ((Image IN ("*\\powershell.exe", "*\\pwsh.exe")) (CommandLine IN ("*Clear-EventLog*", "*Remove-EventLog*", "*Limit-EventLog*")))
```

## Hospital Ransomware - Shadow Copy or Backup Catalog Deletion

Source: `sigma/hosp_ransom_inhibit_system_recovery.yml`

```spl
(Image="*\\vssadmin.exe" CommandLine="*delete*" CommandLine="*shadows*") OR (Image="*\\vssadmin.exe" CommandLine="*resize*" CommandLine="*shadowstorage*") OR (Image="*\\wmic.exe" CommandLine="*shadowcopy*" CommandLine="*delete*") OR (Image="*\\wbadmin.exe" (CommandLine IN ("*delete catalog*", "*delete systemstatebackup*"))) OR (Image="*\\bcdedit.exe" (CommandLine IN ("*recoveryenabled no*", "*bootstatuspolicy ignoreallfailures*"))) OR ((Image IN ("*\\powershell.exe", "*\\pwsh.exe")) CommandLine="*Win32_ShadowCopy*" CommandLine="*Delete*")
```

## Hospital Ransomware - LSASS Memory Dump via comsvcs or ProcDump

Source: `sigma/hosp_ransom_lsass_dump.yml`

```spl
(Image="*\\rundll32.exe" CommandLine="*comsvcs*" CommandLine="*minidump*") OR (CommandLine="* -ma *" CommandLine="*lsass*")
```

## Hospital Ransomware - Command Run Through PsExec Service

Source: `sigma/hosp_ransom_psexec_service_child.yml`

```spl
ParentImage="*\\PSEXESVC.exe" (Image IN ("*\\cmd.exe", "*\\powershell.exe", "*\\pwsh.exe", "*\\rundll32.exe", "*\\wscript.exe", "*\\cscript.exe"))
```

## Hospital Ransomware - Mass Deployment (Shadow Copies Deleted on 3+ Hosts in 10 Minutes)

Source: `sigma/hosp_ransom_recovery_inhibit_multi_host.yml`

```spl
(Image="*\\vssadmin.exe" CommandLine="*delete*" CommandLine="*shadows*") OR (Image="*\\wmic.exe" CommandLine="*shadowcopy*" CommandLine="*delete*")

| bin _time span=10m
| stats dc(Computer) as value_count by _time

| search value_count >= 3
```

## Hospital Ransomware - Backup, Security or Database Services Stopped

Source: `sigma/hosp_ransom_stop_backup_av_db_services.yml`

```spl
(Image IN ("*\\net.exe", "*\\net1.exe", "*\\sc.exe", "*\\taskkill.exe")) (CommandLine IN ("*stop*", "*config*", "*/im*", "*/f*")) (CommandLine IN ("*veeam*", "*backup*", "*acronis*", "*sql*", "*oracle*", "*vss*", "*sophos*", "*defend*", "*sentinel*", "*crowdstrike*", "*csfalcon*", "*mbam*"))
```
