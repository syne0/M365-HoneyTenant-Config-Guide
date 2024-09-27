# M365-HoneyTenant-Config-Guide
## Purpose
The purpose of this guide is to detail some recommendations and best practice for configurations when setting up a Microsoft 365 tenant as a honeypot.

It details the settings required to lock the tenant down as secure as possible, but also provides some examples where settings may need to be adjusted to let the honeypot function, and how you can still keep things relatively secure.
## Disclaimer
Don’t screw up. Don’t play around if you don’t know what you're doing and aren’t prepared for the consequences. Do not tie your honeypot tenant to your dev or prod tenant. Don’t put in similar domains, send emails between them, etc. 

Usage of this guide means that you accept the terms of the associated licence.
## Getting Started
### Provisioning Tenant
There are many ways you can obtain a tenant, it doesnt matter what you choose at the end of the day. If you can access the Microsoft 365 developer program do that, otherwise, budget for a licence that contains Entra ID Premium P1 as a feature, at the very least. Obviously using E5 licencing is perfered for ease of securing and having maximum features available, but work with what you have.

Your administrative account should have a licence, so should any of the accounts you will be allowing threat actors to access.

Bare minimam licencing I would suggest for the HoneyAccount would be business basic. Fun fact, as long as one account within the tenant is licenced for Entra ID P1/P2, most features will apply to the entire tenant. So if you're really strapped for cash, toss an Entra ID P1 licence on your admin account and use business basic for others. You'll want your administrator account to be mail enabled though, so at the very least use Exchange Online Plan 1. I still suggest Business Basic for the admin account as well at the very least.
