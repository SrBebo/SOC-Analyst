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
