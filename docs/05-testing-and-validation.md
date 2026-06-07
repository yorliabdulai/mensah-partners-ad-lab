# 05 — Testing and Validation

## Objective

Verify that all GPOs are enforcing correctly by logging in as users 
from different departments and testing access to restricted features.

## Test Environment

- Domain Controller: MENSAH-DC01 (192.168.10.1)
- Client Workstation: joined to mensahpartners.local
- Test users: akosua.mensah (Finance), yaw.darko (IT)

## Network Connectivity Test

Before domain join, verified communication between DC and client:
ping 192.168.10.2 (from DC)
Reply from 192.168.10.2: bytes=32 time=1ms TTL=128
Packets: Sent=4, Received=4, Lost=0

**Issue encountered:** Initial ping failed. Client was receiving a 
DHCP address from VMware's built-in DHCP service instead of using 
the configured static IP. Resolved by disabling VMware DHCP on 
VMnet1 via Virtual Network Editor and reapplying static IP settings 
on the client.

## Domain Join

Client machine joined to `mensahpartners.local` using Domain 
Administrator credentials. Machine appeared in Active Directory 
Users and Computers under the Computers container immediately after 
joining.

DNS configuration was critical here — the client's preferred DNS 
must point to the DC (192.168.10.1), not a public DNS server. 
Public DNS servers have no knowledge of `mensahpartners.local` and 
will fail to resolve it, causing the domain join to fail.

## GPO Enforcement Tests

### Test User 1 — akosua.mensah (Finance)

Login: `mensahpartners\akosua.mensah`

| Test                        | Expected Result | Actual Result | Pass/Fail |
|-----------------------------|-----------------|---------------|-----------|
| Open Command Prompt         | Blocked         | Blocked       | ✓ Pass    |
| Open Control Panel          | Blocked         | Blocked       | ✓ Pass    |
| Open Registry Editor        | Blocked         | Blocked       | ✓ Pass    |

All Finance GPOs enforcing correctly.

### Test User 2 — yaw.darko (IT)

Login: `mensahpartners\yaw.darko`

| Test                        | Expected Result | Actual Result | Pass/Fail |
|-----------------------------|-----------------|---------------|-----------|
| Open Command Prompt         | Accessible      | Accessible    | ✓ Pass    |
| Open Control Panel          | Accessible      | Accessible    | ✓ Pass    |
| Run whoami in CMD           | Returns domain  | mensahpartners\yaw.darko | ✓ Pass |

IT GPO correctly less restrictive than Finance.

## Key Observations

**GPOs apply at login.** Group Policy is processed when a user 
logs in. Changes to GPOs take effect at next login or can be 
forced immediately with `gpupdate /force` in CMD on the client 
machine.

**OU placement determines policy.** A user receives the GPO of 
the OU they are placed in. Moving a user to a different OU changes 
which policies apply to them at next login.

**Testing is non-negotiable.** A misconfigured GPO pushed to 
production without testing can lock out entire departments 
simultaneously. Standard practice is to test on a single machine 
or pilot OU before broad deployment.

## What Was Not Tested

- Removable storage block (requires physical USB in VM environment)
- HR and Management GPO enforcement (extend testing by logging 
  in as abena.osei and kweku.mensah respectively)
- Fine-grained password policies per OU
- Shared folder NTFS permissions per Security Group

These are noted as extensions for future iterations of this lab.
