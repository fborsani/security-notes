# Enumeration

## Domain Information

### PowerShell

```
$ADCLass = [System.DirectoryServices.ActiveDirectory.Domain]
$ADClass::GetCurrentDomain() 

Get-WmiObject -Namespace root\directory\ldap -Class ds_domain | select ds_dc, ds_distinguishedname, pscomputername

//get DC
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | where {$_.ds_useraccountcontrol -match 532480} | select ds_cn, ds_dnshostname, ds_operatingsystem

//get servers
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | where {$_.ds_useraccountcontrol -match 4096} | select ds_cn, ds_dnshostname, ds_operatingsystem
```

### PowerView

```
Get-NetDomain
Get-NetDomain -Domain <domain>
Get-DomainSID
Get-DomainPolicy
(Get-DomainPolicy)."system access"
(Get-DomainPolicy)."kerberos policy"
Get-NetDomainController
```

Trust policies

```
Get-NetDomainTrust
Get-NetDomainTrust -Domain <domain>
```

Forest

```
Get-NetForest
Get-NetForest -Forest <forest domain>
Get-NetForestDomain
Get-NetForestDomain -Forest <forest domain>
Get-NetForestCatalog
Get-NetForestCatalog -Forest <forest domain>
Get-NetForestTrust
Get-NetForestTrust -Forest <forest domain>
```

### LDAP Module

```
Get-ADDomain
Get-ADDomain -Identity <domain>
(Get-ADDomain).DomainSid
Get-ADDomainController
```

Trust policies

```
Get-ADTrust
Get-ADTrust -Identity <domain>
```

Forest

```
Get-ADForest
Get-ADForest -Identity <forest domain>
(Get-ADForest).Domains
Get-ADForest | select -ExpandProperty GlobalCatalogs
Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'
```

## Machines

### PowerShell

```
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer
```

### PowerView

```
Get-NetComputer
Get-NetComputer -OperatingSystem "*<OS>*"
Get-NetComputer -Ping
Get-NetComputer -FullData
```

Find shares and files

```
//PowerView
Invoke-ShareFinder -Verbose
Invoke-FileFinder -Verbose    //show files from readable shares
Get-NetFileServer
```

### LDAP Module

```
Get-ADComputer -Filter * -Properties name | select name
Get-ADComputer -Filter 'OperatingSystem like "*<OS>*"' -Properties * | select name.OperatingSystem
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
Get-ADComputer -Filter * -Properties *
```

## User Information

### CMD

these commands need to be run on a machine connected to the domain

```
net user /domain        //list all
net user <user>/domain  //get info on the user
net accounts /domain    //get info on the account policies
```

### PowerShell

```
Get-WmiObject -Class win32_useraccount
Get-WmiObject -Class win32_useraccount | select name, domain, accounttype
```

List of machines where the current user is administrator

```
$pcs = Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | select -ExpandProperty ds_cn
foreach ($pc in $pcs) {
    (Get-WmiObject -Class win32_computersystem -ComputerName $pc -ErrorAction silentlycontinue).name
}
```

### PowerView

```
Get-NetUser
Get-NetUser -Username <username> 
Get-UserProperty                             //List of properties
Get-UserProperty -Properties <property>      //value of property values for all users
Get-UserProperty -Properties description     //might contain credentials
```

Search a string in user properties

```
Find-userField -SearchField <property> -SearchTerm "<str>"
```

Get logged users

```
Get-NetLoggedOn -ComputerName <fqd name>     //requires local admin
Get-LoggedOnLocal -ComputerName <fqd name>
Get-LastLoggedOn -ComputerName <fqd name>    //requires local admin
Get-NetSession -ComputerName <fqd name>      //summary of users and concurrent sessions
```

User Hunting

```
Find-LocalAdminAccess -Verbose            //find all machines where current user is admin
Invoke-CheckLocalAdminAccess              //check if user has admin privilege on given machine
Invoke-UserHunter                         //find machines where current user has sessions
Invoke-UserHunter -GroupName "RDPUsers"   //find machines where user can RDP to
Invoke-UserHunter -CheckAccess            //check for admin access
```

### LDAP Module

```
 Get-ADUser -Filter *                                //default properties
 Get-ADUser -Filter * -Properties *                  //all properties
 Get-ADUser -Filter * -Properties description        //description might contain credentials
 Get-ADUser -Identity <username> -Properties *       
 
 Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *Property | select name    //property list
 
 //If the machine we are running the query from is not part of a domain we need to specify the DC to query
 Get-ADUser -Identity <username> -Server <DC to query> -Properties *
 Get-ADUser -Filter 'Name -like "<SQL style query>"' -Server <DC to query> -Properties *
```

Search a string in user properties

```
Get-ADUser -Filter '<property> like '*<str>* -Properties * | select name,<property>
```

## Groups

### CMD

these commands need to be run on a machine connected to the domain

```
net group /domain            //list all
net group <group> /domain    //get info on group
```

### PowerShell

```
Get-WmiObject -Class win32_groupindomain | foreach {[wmi]$_.partcomponent}
```

Users in group

```
Get-WmiObject -Class win32_groupuser | where {$_.groupcomponent -match '<group>'} | foreach {[wmi]$_.partcomponent}
```

Groups of user

```
Get-WmiObject -Class win32_groupuser | where {$_.partcomponent -match '<user>'} | foreach {[wmi]$_.groupcomponent}
```

### PowerView

```
Get-NetGroup
Get-NetGroup -Domain <domain>
Get-NetGroup -FullData
Get-NetGroup *<str>*
```

Users in group

```
Get-NetGroupMember -GroupName <group> -Recurse
```

Groups of user

```
Get-NetGroup -Username <user>
```

List all groups on a machine. Requires administrative privileges or the machine has to be a domain controller

```
Get-NetLocalGroup -ComputerName <full qualified domain> -ListGroups
Get-NetLocalGroup -ComputerName <full qualified domain> -Recurse
```

### LDAP Module

```
Get-ADGroup -Filter * | select Name
Get-ADGroup -Filter * -Properties *

Get-ADGroup -Filter 'Name -eq "<str>"' -Properties *
Get-ADGroup -Filter 'Name -like "*<str>*"' -Properties *
```

Users in group

```
Get-ADGroupMember -Identity <group> -Recursive
```

Groups of user

```
Get-ADPrincipalGroupMembership -identity <user>
```

## Group Policies (GPO)

### PowerShell

```
gpresult /RV        //get set of policies applied on current machine
```

### PowerView

```
Get-NetGPO
Get-NetGPO | select displayname
Get-NetGPO -ComputerName <fqdn>
Get-NetGPOGroup    //GPOs for restricted groups or users
```

Users under the machine's group policy

```
Find-GPOComputerAdmin -ComputerName <fqdn>        //find users under the machine's GPO
Find-GPOLocation -UserName <username> -Verbose    //find machines that apply GPs to user
```

Organization Units

```
Get-NetOU -FullData
Get-NetOU -FullData | select displayname,gplink    //get GPO name from gplink attr
Get-NetGPO -GPOName "{<GPO name>}"                 //GPOs applied to OU
```

## LDAP Module

```
Get-GPO -All
Get-GPResultantsetOfPolicy -ReportType Html -Path <output path>
```

Organizational Units

```
Get-ADOrganizationalUnit -Filter * -Properties *
Get-GPO -Guid <GPO name>
```
