# RPC

## Login & Enumeration

```
rpcclient -U "" -N <ip>          #anonymous login
rpcclient -U "<user>" -N <ip>    #anonymous login
```

## RPC commands

### Server and shares

```
srvinfo                Server query info
netshareenum           Enumerate shares
netshareenumall        Enumerate all shares
netsharegetinfo        Get Share Info
netsharesetinfo        Set Share Info
netsharesetdfsflags    Set DFS flags
netfileenum            Enumerate open files
netremotetod           Fetch remote time of day
netnamevalidate        Validate sharename
netfilegetsec          Get File security
netsessdel             Delete Session
netsessenum            Enumerate Sessions
netdiskenum            Enumerate Disks
netconnenum            Enumerate Connections
netshareadd            Add share
netsharedel            Delete share
```

### Users and passwords

```
queryuser             Query user info
querygroup            Query group info
queryusergroups       Query user groups
queryuseraliases		  Query user aliases
querygroupmem         Query group membership
queryaliasmem         Query alias membership
queryaliasinfo        Query alias info
deletealias           Delete an alias
querydispinfo         Query display info
querydispinfo2        Query display info
querydispinfo3        Query display info
querydominfo          Query domain info
enumdomusers          Enumerate domain users
enumdomgroups         Enumerate domain groups
enumalsgroups         Enumerate alias groups
enumdomains           Enumerate domains
createdomuser         Create domain user
createdomgroup        Create domain group
createdomalias        Create domain alias
samlookupnames        Look up names
samlookuprids         Look up names
deletedomgroup		    Delete domain group
deletedomuser         Delete domain user
samquerysecobj        Query SAMR security object
getdompwinfo          Retrieve domain password info
getusrdompwinfo       Retrieve user domain password info
lookupdomain          Lookup Domain Name
chgpasswd             Change user password
getdispinfoidx        Get Display Information Index
setuserinfo           Set user info
```
