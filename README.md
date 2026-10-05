# Microsoft-Entra-ID-Identity-and-Access-Lab
Setting up users, admin roles, password reset and Conditional Access in Microsoft Entra ID, with write-up and evidence

# Identity and Access Administration Lab (Microsoft Entra ID, Users, Groups, Roles, SSPR, Password Protection and Conditional Access)

`Microsoft Entra ID` · `Microsoft 365 Admin Center` · `Conditional Access` · `SSPR` · `Privileged Identity Management` · `Microsoft Applied Skills`

## Overview
This lab was hands-on practice with **identity and access management** in a Microsoft Entra tenant, using the Microsoft Applied Skills assessment "Get started with identities and access using Microsoft Entra." Instead of just reading about identity concepts, I had to actually build things: create an internal user and license them, invite an external guest, set up groups, hand out admin roles, configure self-service password reset, tighten password protection, and then work out which Conditional Access policies would really apply to a sign in.

A lot of the tasks were driven by small reference files in a "Contoso Data Editor" window (things like `Password Reset.txt`, `Password Protection.txt`, `Named Locations.csv` and `What If.csv`). So part of the skill here was reading a requirement, finding the right setting in the right portal, and making the tenant match what was asked, rather than clicking around until something looked right.

## Objective
Get comfortable with the day to day admin work behind identity: onboarding users and guests, assigning the least privilege that gets the job done, controlling how people reset passwords and which sign in methods they can use, and checking the effect of Conditional Access before trusting it.

## Result
I passed the assessment and earned the **Microsoft Applied Skills: Get started with identities and access using Microsoft Entra** credential on 5 October 2026. It is online verifiable through Microsoft Learn, and the credential ID is `F40AC48C961266AC`. A copy of the certificate is in the repo under `evidence/`.

## Environment
- **Platform:** Microsoft Applied Skills assessment on Microsoft Learn, with a hosted Windows 11 lab VM
- **Portals used:** Microsoft Entra admin center (`entra.microsoft.com`) and Microsoft 365 admin center
- **Tenant:** Contoso demo tenant (`m365x00266365.onmicrosoft.com`), signed in as the tenant admin
- **Users involved:** Ben Smith (new user), Arlene Huff (external guest), Lynne Robbins, Isaiah Langer and Megan Bowen (existing users)
- **Reference files:** Contoso Data Editor with `Conditional Access Users.csv`, `Named Locations.csv`, `What If.csv`, `Password Protection.txt` and `Password Reset.txt`

## Tools I Used

| Tool | What It Does | Why I Used It |
|------|--------------|----------------|
| **Microsoft Entra admin center** | Central place to manage identities, groups, roles and security settings | Created users and groups, configured SSPR, password protection, authentication methods and reviewed Conditional Access |
| **Microsoft 365 admin center** | Licensing and admin role management for users | Assigned an Office 365 E5 license and gave a user the Billing Administrator role |
| **Privileged Identity Management (PIM)** | Controls and time limits privileged role assignments | Opened the Billing Administrator role assignment flow to pick members at directory scope |
| **Conditional Access What If tool** | Simulates which policies would apply to a given sign in | Worked out which policies would apply and which would not |
| **Contoso Data Editor** | Lab provided requirement and answer files | Read the requirements and recorded the policy and method settings |

## What I Did

### Creating and Licensing a User
1. In Entra ID, used **Create new user** to add an internal user, Ben Smith, with the user principal name `Bens` on the tenant domain and the display name set to match.
2. On the Properties tab, filled in the job information: job title **Accountant** and department **Finance**.
3. Switched to the Microsoft 365 admin center, found Ben in **Active users** and assigned the **Office 365 E5 (no Teams)** license, which showed 4 of 15 licenses available.

### Inviting an External User
1. Used **Invite external user** from the Users page.
2. Entered the guest's email (`a.huff@contoso.com`) and display name (Arlene Huff), left **Send invite message** on, and checked everything on the **Review + invite** tab before sending the invite.

### Building Groups and Assigning Roles
1. Created a **Security** group named `Tech1`, using the New Group form.
2. Created another group where Entra roles could be assigned, then used the Directory roles picker, searched for "report" and selected **Reports Reader**, which can read sign in and audit reports. This was a good example of choosing a narrow role instead of something like Security Administrator.
3. In the Microsoft 365 admin center, opened the role list for Lynne Robbins and ticked **Billing Administrator**, then saved.
4. Went into Privileged Identity Management to set up the Billing Administrator assignment at directory scope. The wizard would not let me continue until at least one member was picked, which was a useful reminder that PIM will not accept an empty assignment.

### Setting Up Self Service Password Reset (SSPR)
1. Created a group called `SSPR` and added Megan Bowen as a member, so SSPR could be scoped to a specific group instead of the whole tenant.
2. Opened **Password reset, Authentication methods** and set the number of methods required to reset a password. Entra showed a warning that security questions are being retired in March 2027, which is a good real world reminder that supported methods change over time.
3. While looking at the security questions options, the form complained that no questions were configured and that at least three were needed, so I made sure the setting matched what the reference file asked for.

### Choosing Authentication Methods
1. Read `Password Reset.txt` in the Data Editor, which listed each method with a Yes or No: Passkey (FIDO2), Microsoft Authenticator, SMS, Temporary Access Pass, Voice Call and Email OTP.
2. Opened **Authentication methods, Policies** and matched the Enabled column to that list. In the final state Passkey (FIDO2), Microsoft Authenticator and Temporary Access Pass were enabled and SMS was left off.
3. Under **Authentication methods, Settings**, checked the **Report suspicious activity** control, which was set to **Microsoft managed** for all users.

### Tightening Password Protection
1. Opened **Password protection** and set **Custom smart lockout** with a lockout threshold of `15` and a lockout duration of `120` seconds.
2. Turned **Enforce custom list** to **Yes** and added `Falcon` to the custom banned password list, so company themed passwords get rejected.
3. Turned on **Password protection for Windows Server Active Directory** and set the mode to **Audit**, which logs what would be blocked without actually blocking anyone yet.

### Reviewing Conditional Access
1. Opened the Conditional Access policy list and found four policies, all created on the same day. `Policy1` and `Policy2` were **On**, and `Policy3` and `Policy4` were **Report-only**.
2. Opened the details pane on a policy to check its state and the users, agents or workload identities and excluded identities it targeted.
3. Ran the **What If** tool and looked at the **Policies that will apply** tab. It found three policies: `Policy2` (require compliant device or require app protection policy, state On) and `Policy3` and `Policy4` (require compliant device, both Report-only). Classic policies are not evaluated by the tool, which the tool itself points out.
4. Recorded which policies apply and their state in the Data Editor table (Policy Name, Applies, State) before submitting.

## What's in This Repo

```
entra-identity-lab/
├── README.md                          # This file
├── evidence/
│   └── microsoft-applied-skills-entra-credential.pdf
└── screenshots/
    ├── 01-create-user-identity.png
    ├── 02-create-user-job-info.png
    ├── 03-assign-office365-e5-license.png
    ├── 04-invite-external-user.png
    ├── 05-new-group-tech1.png
    ├── 06-group-reports-reader-role.png
    ├── 07-billing-admin-role-assignment.png
    ├── 08-pim-select-member.png
    ├── 09-sspr-group-members.png
    ├── 10-password-reset-methods.png
    ├── 11-authentication-methods-policy.png
    ├── 12-password-protection-lockout-and-banned-list.png
    ├── 13-conditional-access-policies.png
    └── 14-what-if-policies-that-apply.png
```

## Skills I Picked Up
- **Onboarding users properly,** creating the account, filling in job details, and licensing it in the right portal instead of treating those as one step.
- **Handling guests safely,** inviting an external user deliberately and reviewing the invite details before sending.
- **Choosing the narrowest role,** picking Reports Reader for a reporting need instead of a broad security admin role, and understanding why PIM exists for privileged roles.
- **Scoping SSPR with a group,** so a feature like password reset can be rolled out to a pilot group before everyone.
- **Matching a configuration to a written requirement,** reading a reference file and making the tenant settings agree with it.
- **Understanding smart lockout and banned passwords,** and why Audit mode is a safer first step than Enforced mode.
- **Reading Conditional Access with the What If tool,** separating policies that are On from ones in Report-only, and checking what would apply before trusting a policy.

## How This Applies in the Real World
Identity is the new perimeter. Most real incidents start with a stolen or guessed password, an over privileged account, or a guest who kept access longer than they should have. The tasks in this lab map directly to the controls that stop those things: least privilege roles, time limited admin access through PIM, strong sign in methods like passkeys and Authenticator instead of SMS, banned password lists, smart lockout against password spraying, and Conditional Access to require a compliant device.

Report-only mode and Audit mode are also worth calling out. In a real tenant you almost never switch a strict control on for everyone at once. You watch what it would have blocked first, then enforce it, so you do not lock out the CEO on a Monday morning.

## Where I'm Coming From
I'm making the jump into cybersecurity from a background in **healthcare**. Access control is something I already understood on the job, because only the right people should see a patient's information and every access should be explainable. Entra is that same idea applied to a whole organisation's systems. I'm currently studying for **CompTIA Security+** and building labs like this one to get real hands on practice, since that's the gap on paper compared to my experience.

## What I Want to Learn Next
- Building Conditional Access policies from scratch, including named locations and requiring MFA for admins
- Setting up PIM with eligible assignments, approvals and time limits
- Reading Entra sign in logs and audit logs to investigate a suspicious sign in
- Exploring access reviews so guest and admin access gets checked regularly

## Limitations & What I'd Do Differently in Production
- **This was a guided demo tenant,** so there was no real user impact. In production every change would go through a change process and a rollback plan.
- **Password protection for Windows Server Active Directory was set to Audit.** In a real environment I would review the audit results first and then move to Enforced.
- **The custom banned password list was very small.** A real list would be built from company names, products, locations and common local patterns.
- **SMS and Voice Call are weaker methods,** and I would aim to phase them out completely in favour of passkeys and the Authenticator app.
- **The What If tool does not evaluate classic policies,** so a real review would also check for any older policies still in place.

## References
- Microsoft Applied Skills credential (ID `F40AC48C961266AC`), verifiable on Microsoft Learn
- [Microsoft Entra documentation](https://learn.microsoft.com/entra/)
- [Conditional Access overview](https://learn.microsoft.com/entra/identity/conditional-access/overview)
- [Self service password reset](https://learn.microsoft.com/entra/identity/authentication/concept-sspr-howitworks)
- [Password protection](https://learn.microsoft.com/entra/identity/authentication/concept-password-ban-bad)
- [Privileged Identity Management](https://learn.microsoft.com/entra/id-governance/privileged-identity-management/pim-configure)
- [CompTIA Security+ (SY0-701) Exam Objectives](https://www.comptia.org/certifications/security)
