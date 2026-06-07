# 02 — Active Directory Design

## Business Context

Mensah & Partners is a fictional Ghanaian accounting firm with 20 
staff across four departments. Before this implementation, each 
computer ran local user accounts with no central management, no 
password enforcement, no access controls and no way to know who accessed what. 

The goal of this implementation is to centralise identity management, 
enforce security policies, and control access to sensitive financial 
data — bringing the firm into compliance with Ghana's Data Protection 
Act.

## Architecture Decisions

### Why Active Directory

Active Directory provides a single point of control for all user 
accounts, machines, and security policies across the organisation. 
Instead of managing each computer individually, the Domain Controller 
becomes the central authority that all machines report to.

### Domain Name

Internal domain: `mensahpartners.local`

The `.local` suffix is convention for internal domains not exposed 
to the internet. Using a real public domain name for an internal AD 
environment can cause DNS conflicts with external services.

### Single Domain Controller

For a 20-person firm, a single DC is appropriate. In larger 
organisations, a secondary DC would be added for redundancy — if 
the primary DC fails, authentication and Group Policy would continue 
functioning. This is noted as a future improvement for this lab.

### Static IP Requirement

The Domain Controller is assigned a static IP (192.168.10.1). This 
is non-negotiable — domain-joined machines store the DC's IP for 
authentication and DNS resolution. If the IP changes, machines lose 
contact with the domain and users cannot log in.

### DNS

The DC runs its own DNS server, automatically configured during 
Active Directory promotion. All domain-joined machines point to the 
DC for DNS. Public DNS servers (e.g. 8.8.8.8) have no knowledge of 
`mensahpartners.local` and cannot resolve internal domain queries.

## OU Structure
mensahpartners.local
├── Finance
├── HR
├── IT
└── Management

OUs mirror the firm's department structure. This allows different 
Group Policy Objects to be applied per department — Finance gets 
stricter controls than IT, for example.

## Security Group Design

Security Groups follow the AGDLP model:
- User **A**ccounts are placed in **G**lobal groups
- Global groups are placed in **D**omain **L**ocal groups
- Domain Local groups receive **P**ermissions on resources

| Security Group    | OU         | Members                          |
|-------------------|------------|----------------------------------|
| Finance-Staff     | Finance    | akosua.mensah, kwame.asante      |
| HR-Staff          | HR         | abena.osei, kofi.boateng         |
| IT-Staff          | IT         | yaw.darko, abdulai.iddrisu           |
| Management-Staff  | Management | kweku.mensah, adwoa.asante       |

## GPO Design Summary

| Policy Level  | GPO                   | Applies To      |
|---------------|-----------------------|-----------------|
| Domain        | Default Domain Policy | All users       |
| OU            | Finance-Policy        | Finance OU      |
| OU            | HR-Policy             | HR OU           |
| OU            | IT-Policy             | IT OU           |
| OU            | Management-Policy     | Management OU   |
