![image](https://github.com/user-attachments/assets/4be28242-f0c3-466e-8c38-044220b8e77b)

- Conocer inicios de sesión de un usuario en que dispositivo y el país en el que inició sesión
```bash
SigninLogs
| where TimeGenerated > ago(20d)
| where UserPrincipalName == "user@dominio.com"
| extend CountryOrRegion = tostring(LocationDetails.countryOrRegion)
| where ResultDescription contains "Success" or ResultType == 0 
| project TimeGenerated, UserPrincipalName, Location, IPAddress, DeviceDetail.operatingSystem, DeviceDetail.displayName, AppDisplayName
| sort by TimeGenerated desc
```

- Conocer inicios de sesión fallidos de un usuario en que dispositivo y el país en el que no pudo iniciar sesión
```bash
SigninLogs
| where TimeGenerated > ago(5d)
| where UserPrincipalName == "user@dominio.com"
| extend CountryOrRegion = tostring(LocationDetails.countryOrRegion)
| where ResultDescription !contains "Success" or ResultType == 1 
| project TimeGenerated, UserPrincipalName, Location, IPAddress, DeviceDetail.operatingSystem, DeviceDetail.displayName, AppDisplayName
| sort by TimeGenerated desc
```
- Investigar
```bash
search in (SecurityEvent,SecurityAlert,A*) "err"
```

- Actividad asociada a una cuenta de usuario especifica
```bash
let suspiciousAccounts = datatable(account: string) [
  @"NA\timadmin", 
  @"NT AUTHORITY\SYSTEM"
];
SecurityEvent  
| where TimeGenerated > ago(1h)
| where Account in (suspiciousAccounts)
```

- Conocer el nombre de un proceso y el proceso
```bash
SecurityEvent  
| where TimeGenerated > ago(1h) 
| where ProcessName != "" and Process != "" 
| extend StartDir =  substring(ProcessName,0, string_size(ProcessName)-string_size(Process)) 
| order by StartDir desc, Process asc 
| project-away ProcessName
```

- Obtener el nombre de un proceso, en que computadora se ejecutó y cuantas veces
```bash
SecurityEvent  
| where TimeGenerated > ago(1h) and EventID == 4688  
| summarize count() by Process, Computer
``` 

- Cuenta bloqueada
```bash
SecurityEvent
| where TimeGenerated > ago(20d)
| where EventID == "4740"
| where TargetUserName contains "username"
| summarize by TimeGenerated, Activity, TargetUserName, SubjectUserName, TargetDomainName, Computer
| sort by TimeGenerated
``` 
