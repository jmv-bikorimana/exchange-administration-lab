# Exchange PowerShell Administration Commands

## Purpose

This document provides commonly used Microsoft Exchange PowerShell commands for mailbox administration, mail flow management, connector review, monitoring, troubleshooting, and operational support.

---

# Exchange Server Information

List Exchange servers:

```powershell
Get-ExchangeServer
```

Check Exchange version:

```powershell
Get-ExchangeServer | Select Name,Edition,AdminDisplayVersion
```

---

# Mailbox Management

List all mailboxes:

```powershell
Get-Mailbox
```

Get mailbox details:

```powershell
Get-Mailbox username | Format-List
```

Create mailbox for existing AD user:

```powershell
Enable-Mailbox -Identity username
```

Disable mailbox:

```powershell
Disable-Mailbox -Identity username
```

Remove mailbox:

```powershell
Remove-Mailbox -Identity username
```

---

# Mailbox Statistics

Check mailbox size:

```powershell
Get-MailboxStatistics username |
Select DisplayName,ItemCount,TotalItemSize,LastLogonTime
```

List largest mailboxes:

```powershell
Get-MailboxStatistics |
Sort-Object TotalItemSize -Descending |
Select DisplayName,TotalItemSize,ItemCount -First 20
```

---

# Shared Mailbox Management

Create shared mailbox:

```powershell
New-Mailbox -Shared -Name "Shared Mailbox"
```

Grant full access:

```powershell
Add-MailboxPermission `
-Identity "Shared Mailbox" `
-User username `
-AccessRights FullAccess
```

Grant Send As permission:

```powershell
Add-RecipientPermission `
-Identity "Shared Mailbox" `
-Trustee username `
-AccessRights SendAs
```

Review permissions:

```powershell
Get-MailboxPermission "Shared Mailbox"
```

---

# Distribution Group Management

List distribution groups:

```powershell
Get-DistributionGroup
```

Create distribution group:

```powershell
New-DistributionGroup -Name "IT Team"
```

Add member:

```powershell
Add-DistributionGroupMember -Identity "IT Team" -Member username
```

Remove member:

```powershell
Remove-DistributionGroupMember -Identity "IT Team" -Member username
```

View members:

```powershell
Get-DistributionGroupMember "IT Team"
```

---

# Mail Flow

Test mail flow:

```powershell
Test-Mailflow
```

View queues:

```powershell
Get-Queue
```

View messages in queue:

```powershell
Get-Message
```

Retry queue:

```powershell
Retry-Queue -Identity QueueIdentity
```

Remove message from queue:

```powershell
Remove-Message -Identity MessageIdentity
```

---

# Message Tracking

Search sent messages:

```powershell
Get-MessageTrackingLog `
-Sender user@company.com `
-Start "01/01/2026" `
-End "01/02/2026"
```

Search received messages:

```powershell
Get-MessageTrackingLog `
-Recipients user@company.com `
-Start "01/01/2026" `
-End "01/02/2026"
```

Search failed messages:

```powershell
Get-MessageTrackingLog `
-EventId FAIL `
-Start "01/01/2026" `
-End "01/02/2026"
```

---

# Connectors

View send connectors:

```powershell
Get-SendConnector
```

View receive connectors:

```powershell
Get-ReceiveConnector
```

Review receive connector permissions:

```powershell
Get-ReceiveConnector | Select Name,Bindings,PermissionGroups
```

---

# Mailbox Databases

List databases:

```powershell
Get-MailboxDatabase
```

Check database status:

```powershell
Get-MailboxDatabase -Status |
Select Name,Mounted,DatabaseSize,AvailableNewMailboxSpace
```

Mount database:

```powershell
Mount-Database "Mailbox Database"
```

Dismount database:

```powershell
Dismount-Database "Mailbox Database"
```

---

# Certificates

List Exchange certificates:

```powershell
Get-ExchangeCertificate
```

View certificate details:

```powershell
Get-ExchangeCertificate | Format-List FriendlyName,Thumbprint,Services,NotAfter
```

Enable certificate for services:

```powershell
Enable-ExchangeCertificate `
-Thumbprint THUMBPRINT `
-Services IIS,SMTP
```

---

# Virtual Directories

Check OWA virtual directory:

```powershell
Get-OwaVirtualDirectory | Select Name,InternalUrl,ExternalUrl
```

Check ECP virtual directory:

```powershell
Get-EcpVirtualDirectory | Select Name,InternalUrl,ExternalUrl
```

Check Autodiscover:

```powershell
Get-ClientAccessService | Select Name,AutoDiscoverServiceInternalUri
```

---

# Health Check

Check server health:

```powershell
Get-ServerHealth
```

Check Exchange services:

```powershell
Get-Service *Exchange*
```

Check transport services:

```powershell
Get-Service MSExchangeTransport
```

---

# Troubleshooting Commands

Restart IIS:

```powershell
iisreset
```

Check event logs:

```powershell
Get-EventLog Application -EntryType Error -Newest 20
```

Check transport logs:

```powershell
Get-Queue
```

Check mail flow:

```powershell
Test-Mailflow
```

---

# Expected Outcomes

- Faster Exchange administration
- Improved troubleshooting
- Better mailbox management
- Improved mail flow visibility
- Stronger operational support
