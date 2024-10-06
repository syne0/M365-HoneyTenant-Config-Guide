# M365-HoneyTenant-Config-Guide
![alt text](res/honeytenantlogofulltrans.png)
## Purpose
The purpose of this guide is to detail some recommendations and best practice for configurations when setting up a Microsoft 365 tenant as a honeypot. This is different from the concept of a honeytoken or other early-warning type setup within your primary tenant. The HoneyTenant is intended to be used for a deeper study of threat actor behavior, primarily for threat actor activity post phish or other credential leak.

With that in mind, this guide details some ways to set up and configure the tenant, the settings required to lock the tenant down as secure as possible, and also provides some examples where settings may need to be adjusted to let the honeypot function, and how you can still keep things relatively secure.

## Disclaimer
Don’t screw up. Don’t play around if you don’t know what you're doing and aren’t prepared for the consequences. Do not tie your honeypot tenant to your dev or prod tenant. Don’t put in similar domains, send emails between them, etc. 

Usage of this guide means that you accept the terms of the [associated licence](LICENSE.md).

## Definitions
HoneyTenant: A Microsoft365 tenant created for the sole purpose of researching malicious behavior.
HoneyUser: A Microsoft365 user account created which you will give a malicious actor access to for research purposes.

## Getting Started
### Planning
You'll need a few things to make your honeytenant work that require some thought:

- The name of a fake company or organization.
- A domain name that matches that company.
- Names and pictures of a handful of employees. (just search up a random name generator, and use [this](https://thispersondoesnotexist.com/) site to generate the pictures. This is assuming you aren't going to use the sandbox data available with a dev tenant, as mentioned below.)
- Optional: Create a fake website for your fake company, for extra realism.

Once you have your company, domain, and users, you are ready to create your tenant.

### Provisioning Tenant
There are many ways you can obtain a tenant, it doesn't matter what you choose at the end of the day. If you can access the [Microsoft 365 developer program](https://learn.microsoft.com/en-us/office/developer-program/microsoft-365-developer-program-get-started) do that, otherwise, budget for a licence that contains Entra ID Premium P1 as a feature, at the very least. Obviously using E5 licencing is preferred for ease of securing and having maximum features available, but work with what you have.

Your administrative account should have a licence, so should any of the accounts you will be allowing threat actors to access.

Bare minimum licencing I would suggest for the HoneyAccount would be business basic. Fun fact, as long as one account within the tenant is licensed for Entra ID P1/P2, most features will apply to the entire tenant. So if you're really strapped for cash, toss an Entra ID P1 licence on your admin account and use business basic for others. You'll want your administrator account to be mail enabled though, so at the very least use Exchange Online Plan 1. I still suggest Business Basic for the admin account as well at the very least, as this will allow you to access documents within OneDrive and SharePoint.

### Creating Accounts
If you create a tenant via Microsoft's developer platform, you'll have the option to load some sample user accounts via [instant or configurable sandbox](https://learn.microsoft.com/en-us/office/developer-program/microsoft-365-developer-program-get-started#instant-sandbox). Choose the sandbox option that works best for you.

If you are not using the developer tenant, or don't want to use the sandbox, you'll need to create the users and groups manually. This is when you'd use the names you came up with earlier. You can create the users in a variety of ways, just choose whatever way you are the most familiar with.

I personally save the usernames and passwords in a spreadsheet. I know, sue me. I then use that spreadsheet to track what phishing URLs (and other information if I can, such as the kit) are involved with each HoneyUser. 

### Set up domain
You'll need to load the domain you purchased into your tenant, then set your administrator account and any user accounts you created to use that domain as it's primary. Make sure you have enabled privacy on your domain whois as well.

## Essential Settings
### Security Defaults and MFA
I suggest that you disable [Security Defaults](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults) which will be on by default in new tenants. Make sure you enforce MFA for your administrative account via either [Conditional Access Policies](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-admin-mfa) or [Per-User MFA settings](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults). I personally leave MFA disabled for my HoneyUsers, but it's reasonable to enforce MFA for a subset of users if you want to test the capabilities of various types of phishing kits and MFA bypass methods.

### Auditing and Oversight
Ensure you [enable auditing](https://learn.microsoft.com/en-us/purview/audit-log-enable-disable?tabs=microsoft-purview-portal#turn-on-auditing) via the Unified Audit Log.

Grant your administrative account delegated access to any accounts created. This just lets you access the mailbox and poke around.

Place a [litigation hold](https://learn.microsoft.com/en-us/purview/ediscovery-create-a-litigation-hold) on HoneyUser accounts, if you have the right licencing. This just ensures that you don't lose any interesting emails that might have been purged, but can recover them easier than going through eDiscovery.

### Spam Control
If you are using a developer tenant, it's very important that you prevent phishing and spam from being sent outbound. Allowing your HoneyTenant to be used to send phishing is an easy way to get Microsoft to shut it down.

To accomplish this, I create a few [mail flow/transport rules](https://learn.microsoft.com/en-us/exchange/security-and-compliance/mail-flow-rules/manage-mail-flow-rules).

#### Block Outbound
This will prevent spam and phishing from being sent out, and is the most important rule you will create. I still allow email to be sent intra-org since I consider it not a risk, but you can also apply the rule is the sender is internal, instead of the recipient being external.

![alt text](OutboundSettings1.png)

Then, depending on how you want to manage things, I suggest setting your rule to do one of the following 3 things.

![alt text](res/OutboundSettings2.png)

For the second setting, you can also redirect the message to a specific mailbox, similar to how I forward the message for approval to my admin account. I prefer to use the top setting, since it's easy for me release emails on an individual basis if I want to. 

#### Restrict Inbound
When I first created my HoneyTenant, I only created the rule to block outbound mail, as well as a rule to BCC all inbound and outbound email with a blackhole-type mailbox. That worked well for a few weeks, but then threat actors started to use the HoneyUsers to test the deliverability of their phishing emails though M365 spam filters. Oops.

So, that's a risk. There has also been a few times when the HoneyUser has been used to create an account with an external service or website, which the threat actor then uses for a malicious purpose. This activity is definitely useful and fascinating, but I want to ensure it only happens when I have the time to monitor the tenant closely. If I'm not going to be around for a weekend, I'll enable this rule to avoid any of the prior-mentioned shenanigans. 

![alt text](res/InboundSettings1.png)

Same as with the outbound rule, the exact actions you take depend on personal preference. This is what I have it set to do in my tenant.

![alt text](res/InboundSettings2.png)

Toggle this rule as you need, but I do suggest not leaving your phished accounts to have unrestricted inbound mail flow if you aren't around to pull the plug on any malicious activity.
