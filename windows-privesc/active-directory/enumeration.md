# Enumeration

### Domain Information

```
$ADCLass = [System.DirectoryServices.ActiveDirectory.Domain]
$ADClass::GetCurrentDomain() 

//PowerView
Get-NetDomain
Get-NetDomain -Domain <domain>
Get-DomainSID
Get-DomainPolicy
(Get-DomainPolicy)."system access"
(Get-DomainPolicy)."kerberos policy"
Get-NetDomainController

//LDAP module
Get-ADDomain
Get-ADDomain -Identity <domain>
(Get-ADDomain).DomainSid
Get-ADDomainController
```

Trust policies

```
//powerview
Get-NetDomainTrust
Get-NetDomainTrust -Domain <domain>

//LDAP module
Get-ADTrust
Get-ADTrust -Identity <domain>
```

Forests

```
//powerview
Get-NetForest
Get-NetForest -Forest <forest domain>
Get-NetForestDomain
Get-NetForestDomain -Forest <forest domain>
Get-NetForestCatalog
Get-NetForestCatalog -Forest <forest domain>
Get-NetForestTrust
Get-NetForestTrust -Forest <forest domain>

//LDAP module
Get-ADForest
Get-ADForest -Identity <forest domain>
(Get-ADForest).Domains
Get-ADForest | select -ExpandProperty GlobalCatalogs
Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'
```

### User Information

```
//PowerView 
Get-NetUser
Get-NetUser -Username <username> 
Get-UserProperty                             //List of properties
Get-UserProperty -Properties <property>      //value of property values for all users

//LDAP module  
Get-ADUser -Filter *                      //default properties (same as powerView)
Get-ADUser -Filter * -Properties *        //all properties
Get-ADUser -Identity <username> -Properties *
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *Property | select name    //property list
```

Search a string in user properties

```
//PowerView
Find-userField -SearchField <property> -SearchTerm "<str>"

//LDAP Module
Get-ADUser -Filter '<property> like '*<str>* -Properties * | select name,<property>
```

Get logged users

```
//PowerView
Get-NetLoggedOn -ComputerName <fqd name>     //requires local admin
Get-LoggedOnLocal -ComputerName <fqd name>
Get-LastLoggedOn -ComputerName <fqd name>    //requires local admin
Get-NetSession -ComputerName <fqd name>      //summary of users and concurrent sessions
```

User Hunting (PowerView)

```
Find-LocalAdminAccess -Verbose            //find all machines where current user is admin
Invoke-CheckLocalAdminAccess              //check if user has admin privilege on given machine
Invoke-UserHunter                         //find machines where current user has sessions
Invoke-UserHunter -GroupName "RDPUsers"   //find machines where user can RDP to
Invoke-UserHunter -CheckAccess            //check for admin access
```

### Groups

```
//PowerView
Get-NetGroup
Get-NetGroup -Domain <domain>
Get-NetGroup -FullData
Get-NetGroup *<str>*

//LDAP Module
Get-ADGroup -Filter * | select Name
Get-ADGroup -Filter * -Properties *
Get-ADGroup -Filter 'name like "*<str>*"' | select Name
```

Users in group and groups of user

```
//PowerView
Get-NetGroupMember -GroupName <group> -Recurse
Get-NetGroup -Username <user>

//LDAP Module
Get-ADGroupMember -Identity <group> -Recursive
Get-ADPrincipalGroupMembership -identity <user>
```

List all groups on a machine. Requires administrative privileges or the machine has to be a domain controller

```
//PowerView
Get-NetLocalGroup -ComputerName <full qualified domain> -ListGroups
Get-NetLocalGroup -ComputerName <full qualified domain> -Recurse
```

### Group Policies (GPO)

```
//Powershell
gpresult /RV        //get set of policies applied on current machine

//PowerView
Get-NetGPO
Get-NetGPO | select displayname
Get-NetGPO -ComputerName <fqdn>
Get-NetGPOGroup    //GPOs for restricted groups or users

//LDAP Module
Get-GPO -All
Get-GPResultantsetOfPolicy -ReportType Html -Path <output path>
```

Users under the machine's group policy

```
//Powerview
Find-GPOComputerAdmin -ComputerName <fqdn>        //find users under the machine's GPO
Find-GPOLocation -UserName <username> -Verbose    //find machines that apply GPs to user
```

Organization Units

```
//Powerview
Get-NetOU -FullData
Get-NetOU -FullData | select displayname,gplink    //get GPO name from gplink attr
Get-NetGPO -GPOName "{<GPO name>}"                 //GPOs applied to OU

//LDAP Module
Get-ADOrganizationalUnit -Filter * -Properties *
Get-GPO -Guid <GPO name>
```

### Machines

```
//PowerView
Get-NetComputer
Get-NetComputer -OperatingSystem "*<OS>*"
Get-NetComputer -Ping
Get-NetComputer -FullData

//LDAP Module
Get-ADComputer -Filter * -Properties name | select name
Get-ADComputer -Filter 'OperatingSystem like "*<OS>*"' -Properties * | select name.OperatingSystem
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
Get-ADComputer -Filter * -Properties *
```

Find shares and files

```
//PowerView
Invoke-ShareFinder -Verbose
Invoke-FileFilder -Verbose    //show files from readable shares
Get-NetFileServer
```

