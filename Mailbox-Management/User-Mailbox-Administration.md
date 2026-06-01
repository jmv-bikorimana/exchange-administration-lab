# User Mailbox Administration Guide

## Purpose

This document provides standard procedures for creating, managing, modifying, and troubleshooting user mailboxes in Microsoft Exchange environments.

---

# Scope

Applicable to:

- User Mailboxes
- Shared Mailboxes
- Distribution Groups
- Resource Mailboxes
- Mail-Enabled Users

---

# New User Provisioning Process

Workflow:

```text
HR Request
     │
     ▼
Create AD Account
     │
     ▼
Assign Groups
     │
     ▼
Create Mailbox
     │
     ▼
Assign License (if applicable)
     │
     ▼
Provide Credentials
     │
     ▼
User Validation
```

---

# Create New Mailbox

Verify user exists:

```powershell
Get-ADUser username
```

Create mailbox:

```powershell
Enable-Mailbox -Identity username
```

Verify:

```powershell
Get-Mailbox username
```

---

# Create New User and Mailbox

```powershell
New-Mailbox `
-Name "John Doe" `
-Alias jdoe `
-UserPrincipalName jdoe@company.com `
-Password (ConvertTo-SecureString "Password123!" -AsPlainText -Force)
```

Verify:

```powershell
Get-Mailbox jdoe
```

---

# Shared Mailbox Creation

Create shared mailbox:

```powershell
New-Mailbox `
-Shared `
-Name "Finance Shared Mailbox"
```

Verify:

```powershell
Get-Mailbox -RecipientTypeDetails SharedMailbox
```

---

# Grant Mailbox Access

Full Access:

```powershell
Add-MailboxPermission `
-Identity "Finance Shared Mailbox" `
-User username `
-AccessRights FullAccess
```

Send As:

```powershell
Add-RecipientPermission `
-Identity "Finance Shared Mailbox" `
-Trustee username `
-AccessRights SendAs
```

---

# Distribution Group Management

Create group:

```powershell
New-DistributionGroup `
-Name "IT Team"
```

Add member:

```powershell
Add-DistributionGroupMember `
-Identity "IT Team" `
-Member username
```

Verify:

```powershell
Get-DistributionGroupMember "IT Team"
```

---

# Password Reset Procedure

Reset AD password:

```powershell
Set-ADAccountPassword `
-Identity username `
-Reset `
-NewPassword (ConvertTo-SecureString "NewPassword123!" -AsPlainText -Force)
```

Require password change:

```powershell
Set-ADUser `
-Identity username `
-ChangePasswordAtLogon $true
```

---

# Unlock User Account

Check lock status:

```powershell
Get-ADUser username -Properties LockedOut
```

Unlock:

```powershell
Unlock-ADAccount username
```

---

# Disable Mailbox

Disable mailbox:

```powershell
Disable-Mailbox username
```

Verify:

```powershell
Get-Mailbox username
```

Expected:

- Mailbox removed
- AD account remains

---

# Remove Mailbox

Remove mailbox permanently:

```powershell
Remove-Mailbox username
```

Use with caution.

---

# Mailbox Size Monitoring

Check mailbox size:

```powershell
Get-MailboxStatistics username |
Select DisplayName,TotalItemSize
```

Identify large mailboxes:

```powershell
Get-MailboxStatistics |
Sort-Object TotalItemSize -Descending
```

---

# Mailbox Troubleshooting

Common Issues:

## User Cannot Send Mail

Verify:

```powershell
Get-Mailbox username
```

Check:

- Quota limits
- Mailbox status
- Send restrictions

---

## User Cannot Receive Mail

Verify:

```powershell
Test-Mailflow
```

Check:

- Mail queues
- Receive connectors
- DNS records

---

## Outlook Connection Issues

Verify:

```powershell
Test-OutlookConnectivity
```

Check:

- Exchange services
- Autodiscover
- DNS resolution

---

# Documentation Requirements

Record:

- User requests
- Mailbox changes
- Group modifications
- Password resets
- Access assignments

---

# Security Best Practices

- Apply least privilege.
- Review shared mailbox permissions regularly.
- Disable unused accounts.
- Enforce password policies.
- Monitor privileged mailbox access.

---

# Expected Outcomes

- Standardized mailbox administration
- Improved user support
- Secure mailbox management
- Consistent provisioning process
- Reduced operational risk
