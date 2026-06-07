# Mensah & Partners — Active Directory Lab

## The Problem

Most small businesses in Ghana run their computers with no central 
management. Everyone uses local accounts, there are no password 
policies, and if a laptop gets stolen, every file on it is accessible 
to whoever picks it up. For a firm handling client financial data, 
that's not just an IT problem — it's a legal one under Ghana's Data 
Protection Act.

This lab simulates what it looks like to fix that properly.

## What I Built

A fully functional Active Directory environment modeled on a fictional 
Ghanaian accounting firm — Mensah & Partners. Built from scratch on 
Windows Server 2022 using VMware Workstation Pro.

**The environment includes:**
- A Windows Server 2022 Domain Controller (MENSAH-DC01)
- Domain: mensahpartners.local
- 4 Organizational Units: Finance, HR, IT, Management
- 8 domain user accounts with department assignments
- 4 Security Groups controlling resource access
- Domain-wide password and account lockout policies
- Department-specific Group Policy Objects (GPOs)
- A Windows 10 workstation joined to the domain
- Full GPO enforcement testing with real user logins

## Department Policies

| Department | USB Blocked | Control Panel Blocked | CMD Blocked | Registry Blocked |
|------------|-------------|----------------------|-------------|------------------|
| Finance    | ✓           | ✓                    | ✓           | ✓                |
| HR         | ✓           | ✓                    | ✗           | ✗                |
| IT         | ✗           | ✗                    | ✗           | ✗                |
| Management | ✓           | ✓                    | ✗           | ✗                |

## Domain-Level Security Policy

- Minimum password length: 10 characters
- Password complexity: enabled
- Password expiry: 90 days
- Password history: 10 passwords remembered
- Account lockout threshold: 5 failed attempts
- Lockout duration: 30 minutes

## The Hardest Part

Getting the environment running took longer than expected. The original 
Windows Server ISO was corrupted — it was the correct file size but 
wouldn't boot. I worked through VirtualBox compatibility issues with 
Windows 11, switched to VMware Workstation Pro, and eventually 
identified the corrupted ISO as the root cause by downloading a fresh 
copy. The lesson: always verify file integrity with a SHA256 checksum, 
not just file size.

## What I Learned

I came into this knowing Active Directory conceptually. Building it 
myself made the concepts real — particularly how DNS underpins 
everything. When the client machine couldn't join the domain, the fix 
wasn't a domain setting, it was pointing DNS to the Domain Controller. 
That single change made everything work. You don't really understand 
that dependency until you've broken it and fixed it yourself.

## Environment

- Host: Dell Precision 5540, Intel Core i7 9th Gen, 32GB RAM, 
  Windows 11
- Hypervisor: VMware Workstation Pro
- Server OS: Windows Server 2022 Standard Evaluation
- Client OS: Windows 10 Pro
- Network: VMnet1 Host-Only (isolated lab network)

## Documentation

Full step-by-step build documentation is in the `/docs` folder:

- [01 - Environment Setup](docs/01-environment-setup.md)
- [02 - Active Directory Design](docs/02-active-directory-design.md)
- [03 - OU and User Configuration](docs/03-ou-and-user-configuration.md)
- [04 - Group Policy Configuration](docs/04-group-policy-configuration.md)
- [05 - Testing and Validation](docs/05-testing-and-validation.md)

## Next Project

Project 2: AWS Infrastructure as Code — deploying a production-grade 
multi-tier architecture using Terraform, modeled on a real Ghanaian 
fintech use case.
