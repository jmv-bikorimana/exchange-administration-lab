# Microsoft Exchange Troubleshooting Guide

## Purpose

This document provides practical troubleshooting procedures for Microsoft Exchange environments, including mail flow, OWA/ECP access, SMTP connectivity, certificates, DNS, mailbox databases, and service health.

---

## Scope

Applicable to:

- Microsoft Exchange Server
- Windows Server
- Active Directory
- IIS
- DNS
- SMTP
- Outlook Web Access
- Exchange Admin Center

---

# Troubleshooting Workflow

```text
Issue Reported
      ↓
Identify Affected Service
      ↓
Check Exchange Services
      ↓
Review Event Logs
      ↓
Validate DNS and Connectivity
      ↓
Check Mail Flow / OWA / ECP
      ↓
Apply Corrective Action
      ↓
Validate Service Recovery
      ↓
Document Resolution
```

---

# 1. Exchange Service Health

Check Exchange services:

```powershell
Get-Service *Exchange*
```

Check server health:

```powershell
Get-ServerHealth
```

Restart a service if required:

```powershell
Restart-Service MSExchangeTransport
```

---

# 2. Mail Flow Issues

## Symptoms

- Users cannot send emails.
- Users cannot receive emails.
- Messages stuck in queue.
- External mail delivery failure.

Check mail flow:

```powershell
Test-Mailflow
```

Check queues:

```powershell
Get-Queue
```

View messages:

```powershell
Get-Message
```

Restart transport service:

```powershell
Restart-Service MSExchangeTransport
```

Check send connector:

```powershell
Get-SendConnector | Format-List Name,AddressSpaces,Enabled,DNSRoutingEnabled
```

Check receive connector:

```powershell
Get-ReceiveConnector | Select Name,Bindings,PermissionGroups
```

---

# 3. Message Tracking

Search messages by sender:

```powershell
Get-MessageTrackingLog -Sender user@company.com -Start "06/01/2026" -End "06/02/2026"
```

Search failed messages:

```powershell
Get-MessageTrackingLog -EventId FAIL -Start "06/01/2026" -End "06/02/2026"
```

---

# 4. OWA or ECP Not Opening

## Symptoms

- HTTP 503 error
- Blank page
- Access denied
- Too many redirects
- OWA/ECP unavailable

Check IIS services:

```powershell
iisreset /status
```

Restart IIS:

```powershell
iisreset
```

Check application pools:

```powershell
Import-Module WebAdministration
Get-ChildItem IIS:\AppPools | Select Name,State
```

Restart Exchange app pools:

```powershell
Restart-WebAppPool MSExchangeOWAAppPool
Restart-WebAppPool MSExchangeECPAppPool
```

Check virtual directories:

```powershell
Get-OwaVirtualDirectory | Select Name,InternalUrl,ExternalUrl
Get-EcpVirtualDirectory | Select Name,InternalUrl,ExternalUrl
```

---

# 5. Certificate Issues

## Symptoms

- Browser certificate warning
- Outlook connection warning
- SMTP TLS issue
- OWA/ECP HTTPS issue

Check certificates:

```powershell
Get-ExchangeCertificate | Format-List FriendlyName,Thumbprint,Services,NotAfter
```

Enable certificate:

```powershell
Enable-ExchangeCertificate -Thumbprint THUMBPRINT -Services IIS,SMTP
```

Verify assigned services:

```powershell
Get-ExchangeCertificate | Select Thumbprint,Services,NotAfter
```

---

# 6. Mailbox Database Issues

## Symptoms

- Mailboxes unavailable
- Database dismounted
- Users cannot access mailbox

Check database status:

```powershell
Get-MailboxDatabase -Status | Select Name,Mounted
```

Mount database:

```powershell
Mount-Database "Mailbox Database"
```

Check database size:

```powershell
Get-MailboxDatabase -Status | Select Name,DatabaseSize,AvailableNewMailboxSpace
```

---

# 7. SMTP Connectivity Testing

Test port 25:

```powershell
Test-NetConnection mail.company.com -Port 25
```

Test port 587:

```powershell
Test-NetConnection mail.company.com -Port 587
```

Check receive connector bindings:

```powershell
Get-ReceiveConnector | Select Name,Bindings
```

---

# 8. DNS and External Mail Issues

Verify required DNS records:

- A Record
- MX Record
- SPF Record
- DKIM Record
- DMARC Record
- PTR Record

Check MX:

```powershell
nslookup -type=mx company.com
```

Check SPF:

```powershell
nslookup -type=txt company.com
```

Check mail host:

```powershell
nslookup mail.company.com
```

---

# 9. Authentication and Login Issues

## Symptoms

- User cannot log in to OWA.
- Outlook asks password repeatedly.
- Account locked.
- Incorrect credentials.

Check AD user:

```powershell
Get-ADUser username -Properties LockedOut,Enabled
```

Unlock account:

```powershell
Unlock-ADAccount username
```

Reset password:

```powershell
Set-ADAccountPassword -Identity username -Reset -NewPassword (ConvertTo-SecureString "NewPassword123!" -AsPlainText -Force)
```

Force password change:

```powershell
Set-ADUser username -ChangePasswordAtLogon $true
```

---

# 10. Event Log Review

Check recent application errors:

```powershell
Get-EventLog Application -EntryType Error -Newest 30
```

Check system errors:

```powershell
Get-EventLog System -EntryType Error -Newest 30
```

---

# 11. Common Root Causes

| Issue | Possible Cause |
|---|---|
| Mail stuck in queue | DNS, send connector, transport service |
| OWA 503 | IIS app pool stopped, Exchange service issue |
| ECP access denied | Virtual directory or authentication problem |
| External mail rejected | SPF, DKIM, DMARC, PTR, relay issue |
| Database unavailable | Database dismounted or storage issue |
| Outlook login issue | AD account lockout, password, Autodiscover |

---

# 12. Post-Resolution Checklist

After fixing the issue:

- Verify affected service is running.
- Test user access.
- Test internal mail flow.
- Test external mail flow.
- Check queues.
- Review event logs.
- Document root cause.
- Recommend preventive action.

---

# Expected Outcomes

- Faster Exchange issue resolution
- Improved mail service availability
- Better operational visibility
- Reduced downtime
- Stronger documentation
- Improved user support
