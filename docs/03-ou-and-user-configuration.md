# 03 — OU and User Configuration

## Creating Organizational Units

OUs were created under `mensahpartners.local` in Active Directory 
Users and Computers. Each OU has "Protect container from accidental 
deletion" enabled — this prevents OUs from being removed without 
first disabling the protection, avoiding costly mistakes in 
production.

OUs created:
- Finance
- HR
- IT
- Management

## Creating Security Groups

One Global Security Group created per OU:

| Group            | Scope  | Type     | Location   |
|------------------|--------|----------|------------|
| Finance-Staff    | Global | Security | Finance OU |
| HR-Staff         | Global | Security | HR OU      |
| IT-Staff         | Global | Security | IT OU      |
| Management-Staff | Global | Security | Management |

**Why Global scope:** Global groups contain users from the same 
domain and are used to organise users by role. They are later 
nested into Domain Local groups which receive permissions on 
resources. This separation keeps access management clean and 
auditable.

## Creating User Accounts

Eight domain user accounts created with realistic Ghanaian names. 
All accounts created inside their respective department OUs.

| Full Name       | Username        | Department |
|-----------------|-----------------|------------|
| Akosua Mensah   | akosua.mensah   | Finance    |
| Kwame Asante    | kwame.asante    | Finance    |
| Abena Osei      | abena.osei      | HR         |
| Kofi Boateng    | kofi.boateng    | HR         |
| Yaw Darko       | yaw.darko       | IT         |
| Abdulai Iddrisu     | abdulai.iddrisu    | IT         |
| Kweku Mensah    | kweku.mensah    | Management |
| Adwoa Asante    | adwoa.asante    | Management |

**Password settings at creation:**
- Initial password: Welcome@2026!
- Password never expires: enabled (lab only)
- User must change password at next logon: disabled (lab only)

In a production environment, "Password never expires" would never 
be enabled. Password expiry is a compliance requirement under most 
regulatory frameworks including Ghana's Data Protection Act.

## Adding Users to Security Groups

Each user added to their department Security Group via group 
Properties → Members tab.

Permissions are assigned to Security Groups, never to individual 
user accounts. This ensures access control remains consistent and 
manageable as the organisation grows. Adding or removing a user's 
access to resources is done by adding or removing them from the 
relevant group — not by modifying individual permissions on every 
folder or resource they access.
