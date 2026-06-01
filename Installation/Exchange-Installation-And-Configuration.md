# Microsoft Exchange Server Installation and Configuration Guide

## Purpose

This document provides standard procedures for installing and configuring Microsoft Exchange Server in an enterprise environment.

---

# Scope

Applicable to:

- Exchange Server 2019
- Active Directory Integrated Environments
- Enterprise Messaging Systems

---

# Prerequisites

Before installation verify:

## Active Directory

- Domain Functional Level supported
- Domain Controller healthy
- DNS functioning correctly

Verify:

```powershell
dcdiag
```

```powershell
repadmin /replsummary
```

---

## Windows Server

Recommended:

- Windows Server 2019
- Latest updates installed

Verify:

```powershell
winver
```

---

## Required Components

Install prerequisites:

```powershell
Install-WindowsFeature Server-Media-Foundation, RSAT-ADDS
```

Additional Exchange prerequisites should be installed according to Microsoft recommendations.

---

# Active Directory Preparation

## Prepare Schema

```powershell
Setup.exe /PrepareSchema /IAcceptExchangeServerLicenseTerms_DiagnosticDataOFF
```

---

## Prepare Active Directory

```powershell
Setup.exe /PrepareAD /OrganizationName:"Organization"
```

---

## Verify Preparation

Check Exchange organization:

```powershell
Get-OrganizationConfig
```

---

# Exchange Installation

Run setup:

```powershell
Setup.exe
```

Select:

- Mailbox Role

Installation completes successfully.

---

# Post Installation Verification

Open Exchange Management Shell:

```powershell
Get-ExchangeServer
```

Verify services:

```powershell
Get-Service *Exchange*
```

Expected:

- Running

---

# Configure Mailbox Database

Check database:

```powershell
Get-MailboxDatabase
```

Mount database:

```powershell
Mount-Database "Mailbox Database"
```

---

# Configure Send Connector

Create connector:

```powershell
New-SendConnector `
-Name "Internet" `
-AddressSpaces * `
-Internet `
-DNSRoutingEnabled $true
```

Verify:

```powershell
Get-SendConnector
```

---

# Configure Receive Connector

Verify:

```powershell
Get-ReceiveConnector
```

Review:

- Port 25
- Authentication
- Permission Groups

---

# DNS Configuration

Required records:

## A Record

```text
mail.company.com
```

## MX Record

```text
company.com → mail.company.com
```

## SPF Record

```text
v=spf1 ip4:x.x.x.x -all
```

## DKIM

Configure according to organizational requirements.

## DMARC

```text
v=DMARC1; p=none;
```

---

# Certificate Configuration

Verify certificate:

```powershell
Get-ExchangeCertificate
```

Assign certificate:

```powershell
Enable-ExchangeCertificate
```

Services:

- IIS
- SMTP

---

# Mail Flow Testing

Test:

```powershell
Test-Mailflow
```

Verify:

- Internal mail
- External mail
- Queue status

Check queue:

```powershell
Get-Queue
```

---

# Monitoring

Review:

```powershell
Get-ServerHealth
```

```powershell
Get-MailboxDatabaseCopyStatus
```

```powershell
Get-Queue
```

---

# Backup Considerations

Verify:

- Mailbox database backups
- Exchange configuration backups
- Recovery procedures

Test restoration periodically.

---

# Expected Outcomes

- Successful Exchange deployment
- Functional mail flow
- Secure messaging environment
- Active Directory integration
- Reliable email services
