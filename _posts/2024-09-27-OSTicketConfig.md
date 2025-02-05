---
title: osTicket Part 2 - Post-Installation Configuration
date: 2024-09-27 12:50:00 -0600
categories: [Ticketing Systems, Linux]
tags: [vm, tickets, linux, debian, sql, apache, sla]     # TAG names should always be lowercase
description: This lab covers the post-install configuration of osTicket. Here we will configure agents, roles, and SLAs, so that it is ready for use in an organization.
---

<!-- 
<h2>Video Demonstration</h2>

- ### [YouTube: How To Configure osTicket, post-installation](https://www.youtube.com)
 -->

<h2>Environments and Technologies Used</h2>

- Oracle VM VirtualBox
- Debian
- Terminal
- Apache
- MariaDB

<h2>Operating Systems Used </h2>

- Debian 12.7.0

<h2>Post-Install Configuration Objectives</h2>

- Configuring **Roles**, **Departments**, and **Teams**
- Configuring ticket creation rules and permissions
- Creating agents and users
- Configuring a **Service Level Agreement (SLA)**
- Creating **Help Topics**

<h2>Configuration Steps</h2>

<h3>Part 1 - Accessing Our Ticketing System</h3>

In the last tutorial, we installed the prequisites needed to run  osTicket. We installed Apache, MariaDB, and PHP, and configured these services to work together. We gave our site the domain name `myhelpdesk.com`. Now that we have setup osTicket, we can begin configuring our ticketing system. Navigate to `myhelpdesk.com/scp/admin.php` to login with our admin credentials we created.

`Username: jay`

`Password: Password1`

We can now begin the post-installation configuration.

<h3>Part 2 - Configuring Role</h3>

To create a role, navigate to `Agents -> Roles -> Add New Role`.

<img src="/assets/img/osticketconfig/CreateRole_1.png" alt="image" />

We will give this role the name `Supreme Admin`.

<img src="/assets/img/osticketconfig/CreateRole_2.png" alt="image" />

To assign permissions for this role, go to the permissions tab.

<img src="/assets/img/osticketconfig/CreateRole_3.png" alt="image" />

As the name of this role implies, any user with this role should be given the most permissions. Go ahead and tick all the options in each of the sub tabs.

<img src="/assets/img/osticketconfig/CreateRole_4.png" alt="image" />

<img src="/assets/img/osticketconfig/CreateRole_5.png" alt="image" />

<img src="/assets/img/osticketconfig/CreateRole_6.png" alt="image" />

Click `Add Role`. We should now see this new role on the `Roles` page.

<img src="/assets/img/osticketconfig/CreateRole_7.png" alt="image" />

<h3>Part 3 - Configuring Department</h3>

To create a new department, navigate to `Agents -> Departments -> Add New Department`.

<img src="/assets/img/osticketconfig/CreateDepartment_1.png" alt="image" />

We will call this department `System Administrators`. Leave all other settings as they are.

<img src="/assets/img/osticketconfig/CreateDepartment_2.png" alt="image" />

We will add agents to this department later. For the time being, click `Create Dept`. We should now see the department in the list of all departments.

<img src="/assets/img/osticketconfig/CreateDepartment_3.png" alt="image" />

<h3>Part 4 - Configuring Team</h3>

A team allows us to manage agents from different departments. To create a team, navigate to `Agents -> Teams -> Add New Team`.

<img src="/assets/img/osticketconfig/CreateTeam_1.png" alt="image" />

In a real help desk environment, their will be different ticket escalation levels. As there is already a team for `Level I Support`, let's create a team for `Level II Support`.

<img src="/assets/img/osticketconfig/CreateTeam_2.png" alt="image" />

Again, we will add members to this team later. For now, create the team as is. We should now see the team in the list of all teams.

<img src="/assets/img/osticketconfig/CreateTeam_3.png" alt="image" />

<h3>Part 5 - Configuring Ticket Creation</h3>

In our help desk system, anyone should be able to submit a ticket regardless of if they have registered an account with our service. We can configure ticket creation permissions by navigating to `Settings -> Users`.

<img src="/assets/img/osticketconfig/ConfigureTicketPermissions_1.png" alt="image" />

As we want to allow anyone to submit tickets, we will not force users to create an account. Furthermore, anyone should be able to register for an account if they so choose. With these rules in place, we can save these changes.

<h3>Part 6 - Creating Agents</h3>

To create an agent, navigate to `Agents -> Add New Agent`

<img src="/assets/img/osticketconfig/CreateAgent.png" alt="image" />

We will create two agents, Jane Doe and John Doe. First, let's create Jane, who has been with the company for a long time.

```
Name: Jane Doe
Email: jane.doe@jayhelpdesk.com
Username: jane.doe
Password: Password1
Department: System Administators
Role: Supreme Admin
Team: Level II Support
```

Next, we'll create one for John, who is a new hire.

```
Name: John Doe
Email: john.doe@jayhelpdesk.com
Username: john.doe
Password: Password1
Department: Support
Role: View Only
Team: Level I Support
```

When setting the password, make sure to disable password reset upon first login! Also, if you are getting an error indicating the email address is not valid, you will need to navigate to `Emails -> Settings` and uncheck the following option.

<img src="/assets/img/osticketconfig/DisableEmailVerification.png" alt="image" />

After creation, we should now see these agents in the list of all agents.

<img src="/assets/img/osticketconfig/AgentsCreated.png" alt="image" />

<h3>Part 7 - Creating Users</h3>

Although users would typically sign up through the portal, we can also manually create users. These may be users that have contacted our IT team by email or phone rather than through our ticketing system directly. To create a user, go the the `Agent Panel` and navigate to `Users -> Add User`.

<img src="/assets/img/osticketconfig/CreateUser.png" alt="image" />

From here we will create two users, Ken and Karen.

<img src="/assets/img/osticketconfig/CreateUser_Ken.png" alt="image"   height="40%" width="40%" />
<img src="/assets/img/osticketconfig/CreateUser_Karen.png" alt="image" height="40%" width="40%" />

After creation, we should now see these users in the list of all users.

<img src="/assets/img/osticketconfig/User_Directory.png" alt="image" />

Notice that if we go to any of these users, we have an option to register them for an account.

<img src="/assets/img/osticketconfig/User_Karen.png" alt="image" />

We can choose to send them an email link to register from, or set their account up for them with a temporary password.

<h3>Part 8 - Configuring SLA</h3>

An SLA (Service-Level Agreement) is a commitment between a service provider and a client. Particular aspects of the service – quality, availability, responsibilities – are agreed between the service provider and the service user. Depending on the organization, they might have an SLA in place such that certain tickets must be responded to and/or resolved within a certain time period. With this in mind we can create three different SLA plans:

- **Sev-A** (1 hour, 24/7)
- **Sev-B** (4 hours, 24/7)
- **Sev-C** (8 hours, business hours)

To create these, go to the `Admin Panel` and navigate to `Manage -> SLA -> Add New SLA Plan`.

<img src="/assets/img/osticketconfig/CreateSLA.png" alt="image" />

Once created, we should see these SLA Plans in the list of all plans.

<img src="/assets/img/osticketconfig/SLA_Plans_Created.png" alt="image" />

Now when an agent sees an open ticket, they can assign it a severity level as according to the SLA policy.

<h3>Part 9 - Creating Help Topics</h3>

Help topics allow agents to broadly categorize tickets for better organization and management. To create a help topic, navigate to `Manage -> Help Topics -> Add New Help Topic`.

<img src="/assets/img/osticketconfig/CreateHelpTopic.png" alt="image" />

We will create the following help topics:

- Business Critical Outage
- Personal Computer Issues
- Password Reset
- Equipment Request

We can choose to put these topics under parent topics to better organize. For example, the first three topics can be under the `Report a Problem` topic and the last topic can be under the `General Inquiry` topic.

<img src="/assets/img/osticketconfig/HelpTopicsCreated.png" alt="image" />

Congratulations! We have now completed the post-installation configuration of osTicket and can now begin using our ticketing system.
