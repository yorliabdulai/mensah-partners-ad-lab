# 04 — Group Policy Configuration

## Overview

Group Policy Objects (GPOs) define what users can and cannot do 
on their machines. Policies are applied at two levels:

- **Domain level** — applies to all users and computers
- **OU level** — applies only to users in that specific OU

## Domain-Level Policy — Default Domain Policy

Applies to every user and computer in mensahpartners.local.

### Password Policy

| Setting                                    | Value    |
|--------------------------------------------|----------|
| Enforce password history                   | 10       |
| Maximum password age                       | 90 days  |
| Minimum password age                       | 1 day    |
| Minimum password length                    | 10 chars |
| Password must meet complexity requirements | Enabled  |

**Why minimum password age of 1 day:** Prevents users from cycling 
through passwords rapidly to return to their original one, bypassing 
the password history requirement.

### Account Lockout Policy

| Setting                           | Value       |
|-----------------------------------|-------------|
| Account lockout threshold         | 5 attempts  |
| Account lockout duration          | 30 minutes  |
| Reset account lockout counter     | 30 minutes  |

Account lockout protects against brute force attacks. Without it, 
an attacker can attempt unlimited password combinations 
automatically. After 5 failed attempts the account locks for 30 
minutes.

## OU-Level Policies

### Finance-Policy (Finance OU)

Finance handles client financial data — the strictest policy.

| Setting                                        | Configuration |
|------------------------------------------------|---------------|
| All Removable Storage classes: Deny all access | Enabled       |
| Prohibit access to Control Panel               | Enabled       |
| Prevent access to the command prompt           | Enabled       |
| Prevent access to registry editing tools      | Enabled       |

**Why block CMD and PowerShell for Finance:** Standard users have 
no legitimate need for command line access. A Finance employee with 
CMD access could map network drives to restricted folders, run 
scripts to extract data, or provide an entry point for malware to 
move laterally across the network.

**Why block all removable storage classes, not just USB:** USB is 
one removable storage type. Blocking only USB leaves SD cards, 
external drives, and other media as viable data exfiltration paths. 
Denying all removable storage classes closes every avenue with one 
policy setting.

### HR-Policy (HR OU)

HR handles employee records and payroll data.

| Setting                                        | Configuration |
|------------------------------------------------|---------------|
| All Removable Storage classes: Deny all access | Enabled       |
| Prohibit access to Control Panel               | Enabled       |

HR retains CMD access — they may need to run basic scripts or 
access mapped drives via command line for payroll processing tools.

### IT-Policy (IT OU)

IT administers the entire environment and needs the least 
restrictive policy.

| Setting                | Configuration                    |
|------------------------|----------------------------------|
| Allow log on locally   | IT-Staff + Administrators        |

IT is explicitly exempt from the software installation restriction, 
Control Panel block, and CMD block that apply to other departments. 
Restricting IT's tools would prevent them from doing their job.

**Note:** When configuring User Rights Assignment policies, 
Administrators must always be included explicitly alongside any 
added group. Removing Administrators from logon rights can lock 
everyone out of domain machines entirely.

### Management-Policy (Management OU)

Management needs access to reports across departments but does not 
need to modify system settings.

| Setting                                        | Configuration |
|------------------------------------------------|---------------|
| All Removable Storage classes: Deny all access | Enabled       |
| Prohibit access to Control Panel               | Enabled       |

Management retains CMD access for running queries and scripts 
relevant to business reporting.
