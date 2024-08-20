## from https://forum.ixbt.com/topic.cgi?id=7:45917

== Problem

Predefined user management roles in Exchange 2013 either provide too few rights (Mail Recipient Creation - you can't create a mailbox for an existing AD account) or too many (Mail Recipients - allows you to delete existing mailboxes and AD accounts). It is necessary to give technical support staff the rights to create mailboxes, but not to delete them.

== Solution

Create a new management role that provides only the necessary rights.

The new role must be created as a child of an existing predefined role. The role's rights are determined by the list of Exchange cmdlets to which the role has access. A manually created role can have access to cmdlets that are available to the parent role removed, thus reducing the available rights. The created role must be added to an administrative role to assign the appropriate rights to users (one administrative role can contain several management roles).

An administrative role (in ECP, the Permissions -> Admin roles section) is an AD group to which additional attributes (available management roles) are assigned in Exchange. Exchange groups are by default located in the Microsoft Exchange Security Groups container. Users can be added to them both in ADUC and in ECP.

Manipulations with management roles are performed exclusively through Powershell cmdlets in EMS. You can work with administrative roles both in ECP and in EMS.

Basic commands:

New-ManagementRole - create a new management role
Get-ManagementRoleEntry - view cmdlets assigned to the role
Remove-ManagementRoleEntry - remove role access to the cmdlet

Procedure for creating an administrative role to solve the task:

1) Create a new management role:

New-ManagementRole -Parent "Mail Recipients" -Name "ROLE-NAME"

2) Remove potentially dangerous rights:

Get-ManagementRoleEntry -Identity "ROLE-NAME\*" | Where-Object {($_.Name -like 'Disable*')} | Remove-ManagementRoleEntry

This command removes access to the following cmdlets:

Disable-InboxRule
Disable-MailContact
Disable-MailUser
Disable-RemoteMailbox
Disable-ServiceEmailChannel
Disable-Mailbox

View remaining cmdlets (for control): Get-ManagementRoleEntry -Identity "ROLE-NAME\*"

3) Create a new administrative role in ECP: in the window for creating a new administrative role, add the new role ROLE-NAME in the Roles field and the accounts of technical support employees in the Members field.
