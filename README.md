# M365-HoneyTenant-Config-Guide
![alt text](res/honeytenantlogofulltrans.png)
## Purpose
The purpose of this guide is to detail some recommendations and best practice for configurations when setting up a Microsoft 365 tenant as a honeypot. This is different from the concept of a honeytoken or other early-warning type setup within your primary tenant. The HoneyTenant is intended to be used for a deeper study of threat actor behavior, primarily for threat actor activity post phish or other credential leak.

With that in mind, this guide details some ways to set up and configure the tenant, the settings required to lock the tenant down as secure as possible, and also provides some examples where settings may need to be adjusted to let the honeypot function, and how you can still keep things relatively secure.

## Disclaimer
Don’t screw up. Don’t play around if you don’t know what you're doing and aren’t prepared for the consequences. Do not tie your honeypot tenant to your dev or prod tenant. Don’t put in similar domains, send emails between them, etc. 

Usage of this guide means that you accept the terms of the [associated licence](LICENSE.md).

## Definitions
blah

## Getting Started
### Planning
You'll need a few things to make your honeytenant work that require some thought:

- The name of a fake company or organization
- A domain name that matches that company
- Names and pictures of a handful of employees (just search up a random name generator, and use [this](https://thispersondoesnotexist.com/) site to generate the pictures. This is assuming you aren't going to use the sandbox data available with a dev tenant, as mentioned below.)
- Optional: Create a fake website for your fake company, for extra realism

Once you have your company, domain, and users, you are ready to create your tenant.

### Provisioning Tenant
There are many ways you can obtain a tenant, it doesn't matter what you choose at the end of the day. If you can access the [Microsoft 365 developer program](https://learn.microsoft.com/en-us/office/developer-program/microsoft-365-developer-program-get-started) do that, otherwise, budget for a licence that contains Entra ID Premium P1 as a feature, at the very least. Obviously using E5 licencing is preferred for ease of securing and having maximum features available, but work with what you have.

Your administrative account should have a licence, so should any of the accounts you will be allowing threat actors to access.

Bare minimum licencing I would suggest for the HoneyAccount would be business basic. Fun fact, as long as one account within the tenant is licensed for Entra ID P1/P2, most features will apply to the entire tenant. So if you're really strapped for cash, toss an Entra ID P1 licence on your admin account and use business basic for others. You'll want your administrator account to be mail enabled though, so at the very least use Exchange Online Plan 1. I still suggest Business Basic for the admin account as well at the very least, as this will allow you to access documents within OneDrive and SharePoint.

### Creating Accounts
If you create a tenant via Microsoft's developer platform, you'll have the option to load some sample user accounts via [instant or configurable sandbox](https://learn.microsoft.com/en-us/office/developer-program/microsoft-365-developer-program-get-started#instant-sandbox). Choose the sandbox option that works best for you.

If you are not using the developer tenant, or don't want to use the sandbox, you'll need to create the users and groups manually. This is when you'd use the names you came up with earlier. You can create the users in a variety of ways, just choose whatever way you are the most familiar with.

I personally save the user accounts and passwords in a spreadsheet. I know, sue me. I then use that spreadsheet to track what phishing URLs (and other information if I can, such as the kit) for each HoneyUser. I 

### Set up domain
You'll need to load the domain you purchased into your tenant, then set your administrator account and any user accounts you created to use that domain as it's primary. Make sure you have enabled privacy on your domain whois as well.

## Settings
### Security Defaults and MFA
I suggest that you disable [Security Defaults](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults) which will be on by default in new tenants. Make sure you enfore MFA for your administrative account via either [Conditional Access Policies](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-admin-mfa) or [Per-User MFA settings](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults). I personally leave MFA disabled for my HoneyUsers, but it's reasonable to enforce MFA for a subset of users if you want to test the capabilities of various types of phishing kits and MFA bypass methods.

### Auding and Oversight
Ensure you [enable auditing](https://learn.microsoft.com/en-us/purview/audit-log-enable-disable?tabs=microsoft-purview-portal#turn-on-auditing) via the Unified Audit Log.

Grant your administrative account 

### User Capabilities
