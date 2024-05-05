# Enumeration

## Domain Information

{% tabs %}
{% tab title="PowerShell" %}
```powershell
$ADCLass = [System.DirectoryServices.ActiveDirectory.Domain];$ADClass::GetCurrentDomain();
Get-WmiObject -Namespace root\directory\ldap -Class ds_domain | select ds_dc, ds_distinguishedname, pscomputername

#get DC
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | where {$_.ds_useraccountcontrol -match 532480} | select ds_cn, ds_dnshostname, ds_operatingsystem

#get servers
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | where {$_.ds_useraccountcontrol -match 4096} | select ds_cn, ds_dnshostname, ds_operatingsystem
```
{% endtab %}

{% tab title="PowerView" %}
<pre class="language-python"><code class="lang-python">Get-NetDomain
Get-NetDomain -Domain &#x3C;domain>
Get-DomainSID
<strong>Get-DomainPolicy
</strong><strong>(Get-DomainPolicy)."system access"
</strong>(Get-DomainPolicy)."kerberos policy"
Get-NetDomainController

#Trust policies
Get-NetDomainTrust
Get-NetDomainTrust -Domain &#x3C;domain>

#Forest
Get-NetForest
Get-NetForest -Forest &#x3C;forest domain>
Get-NetForestDomain
Get-NetForestDomain -Forest &#x3C;forest domain>
Get-NetForestCatalog
Get-NetForestCatalog -Forest &#x3C;forest domain>
Get-NetForestTrust
Get-NetForestTrust -Forest &#x3C;forest domain>
</code></pre>
{% endtab %}

{% tab title="LDAP Module" %}
<pre class="language-ruby"><code class="lang-ruby"><strong>#Get-ADDomain
</strong>Get-ADDomain -Identity &#x3C;domain>
(Get-ADDomain).DomainSid
Get-ADDomainController

#Trust policies
Get-ADTrust
Get-ADTrust -Identity &#x3C;domain>

#Forest
Get-ADForest
Get-ADForest -Identity &#x3C;forest domain>
(Get-ADForest).Domains
Get-ADForest | select -ExpandProperty GlobalCatalogs
Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'
</code></pre>
{% endtab %}
{% endtabs %}

## Machines

{% tabs %}
{% tab title="PowerShell" %}
```powershell
Get-WmiObject -Namespace root\directory\ldap -Class ds_computer
```
{% endtab %}

{% tab title="PowerView" %}
```powershell
Get-NetComputer
Get-NetComputer -OperatingSystem "*<OS>*"
Get-NetComputer -Ping
Get-NetComputer -FullData
```

Find shares and files

```powershell
Invoke-ShareFinder -Verbose
Invoke-FileFinder -Verbose    //show files from readable shares
Get-NetFileServer
```
{% endtab %}

{% tab title="LDAP Module" %}
```powershell
Get-ADComputer -Filter * -Properties name | select name
Get-ADComputer -Filter 'OperatingSystem like "*<OS>*"' -Properties * | select name.OperatingSystem
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
Get-ADComputer -Filter * -Properties *
```
{% endtab %}
{% endtabs %}

## User Information

{% tabs %}
{% tab title="CMD" %}
```batch
net user /domain        #list all
net user <user>/domain  #get info on the user
net accounts /domain    #get info on the account policies
```
{% endtab %}

{% tab title="PowerShell" %}
```powershell
Get-WmiObject -Class win32_useraccount
Get-WmiObject -Class win32_useraccount | select name, domain, accounttype
```

List of machines where the current user is administrator

```powershell
$pcs = Get-WmiObject -Namespace root\directory\ldap -Class ds_computer | select -ExpandProperty ds_cn
foreach ($pc in $pcs) {
    (Get-WmiObject -Class win32_computersystem -ComputerName $pc -ErrorAction silentlycontinue).name
}
```
{% endtab %}

{% tab title="PowerView" %}
```powershell
Get-NetUser
Get-NetUser -Username <username> 
Get-UserProperty                             #List of properties
Get-UserProperty -Properties <property>      #value of property values for all users
Get-UserProperty -Properties description     #might contain credentials
```

Search a string in user properties

```powershell
Find-userField -SearchField <property> -SearchTerm "<str>"
```

Get logged users

```powershell
Get-NetLoggedOn -ComputerName <fqd name>     #requires local admin
Get-LoggedOnLocal -ComputerName <fqd name>
Get-LastLoggedOn -ComputerName <fqd name>    #requires local admin
Get-NetSession -ComputerName <fqd name>      #summary of users and concurrent sessions
```

User Hunting

```powershell
Find-LocalAdminAccess -Verbose            //find all machines where current user is admin
Invoke-CheckLocalAdminAccess              //check if user has admin privilege on given machine
Invoke-UserHunter                         //find machines where current user has sessions
Invoke-UserHunter -GroupName "RDPUsers"   //find machines where user can RDP to
Invoke-UserHunter -CheckAccess            //check for admin access
```
{% endtab %}

{% tab title="LDAP Module" %}
```powershell
Get-ADUser -Filter *                                #default properties
Get-ADUser -Filter * -Properties *                 #all properties
Get-ADUser -Filter * -Properties description       #description might contain credentials
Get-ADUser -Identity <username> -Properties *       

#List of properties
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *Property | select name    
 
#If the machine we are running the query from is not part of a domain we need to specify the DC to query
Get-ADUser -Identity <username> -Server <DC to query> -Properties *
Get-ADUser -Filter 'Name -like "<SQL style query>"' -Server <DC to query> -Properties *
```

Search a string in user properties

```powershell
Get-ADUser -Filter '<property> like '*<str>* -Properties * | select name,<property>
```
{% endtab %}
{% endtabs %}

## Groups

{% tabs %}
{% tab title="CMD" %}
```batch
net group /domain            //list all
net group <group> /domain    //get info on group
```
{% endtab %}

{% tab title="PowerShell" %}
```powershell
Get-WmiObject -Class win32_groupindomain | foreach {[wmi]$_.partcomponent}
```

Users in group

```powershell
Get-WmiObject -Class win32_groupuser | where {$_.groupcomponent -match '<group>'} | foreach {[wmi]$_.partcomponent}
```

Groups of user

```powershell
Get-WmiObject -Class win32_groupuser | where {$_.partcomponent -match '<user>'} | foreach {[wmi]$_.groupcomponent}
```
{% endtab %}

{% tab title="PowerView" %}
```powershell
Get-NetGroup
Get-NetGroup -Domain <domain>
Get-NetGroup -FullData
Get-NetGroup *<str>*
```

Users in group

```powershell
Get-NetGroupMember -GroupName <group> -Recurse
```

Groups of user

```powershell
Get-NetGroup -Username <user>
```

List all groups on a machine. Requires administrative privileges or the machine has to be a domain controller

```powershell
Get-NetLocalGroup -ComputerName <full qualified domain> -ListGroups
Get-NetLocalGroup -ComputerName <full qualified domain> -Recurse
```
{% endtab %}

{% tab title="LDAP Module" %}
```powershell
Get-ADGroup -Filter * | select Name
Get-ADGroup -Filter * -Properties *

Get-ADGroup -Filter 'Name -eq "<str>"' -Properties *
Get-ADGroup -Filter 'Name -like "*<str>*"' -Properties *
```

Users in group

```powershell
Get-ADGroupMember -Identity <group> -Recursive
```

Groups of user

```powershell
Get-ADPrincipalGroupMembership -identity <user>
```
{% endtab %}
{% endtabs %}

## Group Policies (GPO)

{% tabs %}
{% tab title="PowerShell" %}
```powershell
gpresult /RV        #get set of policies applied on current machine
```
{% endtab %}

{% tab title="PowerView" %}
```powershell
Get-NetGPO
Get-NetGPO | select displayname
Get-NetGPO -ComputerName <fqdn>
Get-NetGPOGroup    #GPOs for restricted groups or users
```

Users under the machine's group policy

```powershell
Find-GPOComputerAdmin -ComputerName <fqdn>        //find users under the machine's GPO
Find-GPOLocation -UserName <username> -Verbose    //find machines that apply GPs to user
```

Organization Units

```powershell
Get-NetOU -FullData
Get-NetOU -FullData | select displayname,gplink    //get GPO name from gplink attr
Get-NetGPO -GPOName "{<GPO name>}"                 //GPOs applied to OU
```
{% endtab %}

{% tab title="LDAP Module" %}
list GPO

<pre class="language-powershell"><code class="lang-powershell"><strong>Get-GPO -All
</strong>Get-GPResultantsetOfPolicy -ReportType Html -Path &#x3C;output path>
</code></pre>

get details of GPO

```powershell
Get-ADOrganizationalUnit -Filter * -Properties *
Get-GPO -Guid <GPO name>
```
{% endtab %}
{% endtabs %}
