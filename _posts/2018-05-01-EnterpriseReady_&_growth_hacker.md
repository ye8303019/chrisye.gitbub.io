---
layout: post
title:  "Thoughts On EnterpriseReady"
date:   2018-05-01 10:00:11 +0800
author: Ye Zhongkai
categories: Original Article
tags: EnterpriseReady SaaS TOB TOC
---

## Preface
With more and more SaaS companies are set up, some play big, some run small. How to build a enterprise SaaS product? How to keep growth? What's the key feature that a enterprise SaaS product should have? Lucky, there are some cool guys, collect and maintain a guide line for EnterpriseReady SaaS, most content comes from [https://www.enterpriseready.io/](https://www.enterpriseready.io/).

## EnterpriseReady

    Cause that the original content has been written in very detail, 
    so here i just post some personal thinking and important pictures to make the concept 
    more obvious and more simple. 

### Product Assortment
Project Assortment could meet the requirements of different level clients,
include starter, professional and enterprise. Enterprise care about whether the application can be trusted. The HA, FT, SLA, Security are the factor that they concern besides product feature. 
But for individual user, they may not need that many features as enterprise, reasonable product assortment will enable the value capture more proportionally. 
Usually, EnterpriseReady suggest [3 packages for a product](https://www.harrisonmetal.com/library/pricing-3-assortments-are-for-winners), but it's dependents on the business of the application. Differences between each assortment need to be clear. 
![Slack Assortment]({{ site.url }}/images/enterprise_ready/product_assortment/Slack Assortment.png)
![Zendesk Assotment1]({{ site.url }}/images/enterprise_ready/product_assortment/Zendesk Assotment1.png)
![Zendesk Assotment2]({{ site.url }}/images/enterprise_ready/product_assortment/Zendesk Assotment2.png)
Product Assortment is widely used in many industry. ie. Final Fantasy XXV have 3 editions, Normal Edition, Deluxe Edition and Royal Edition, if you want to play all DLC, then you need to pay extra money.
![FFXV]({{ site.url }}/images/enterprise_ready/product_assortment/FF15-Royal.png)

### Single Sign On 
It's unreasonable for a enterprise to maintain several account info, so product need to have ability to integrate with most popular SAML Ids or OAuth.
Product need to provide a configuration page for enterprise administrator to do the provisioning and deprovisioning, also a clear documentation is needed.
Be careful and honors to the deep-linked content.
Here are some illustrations from "Password Manager Pro", the documentation guide user to integrate with Okta, a SAML Ids.  
![SAML]({{ site.url }}/images/enterprise_ready/sso/sso4.jpg)
![SAML]({{ site.url }}/images/enterprise_ready/sso/sso5.jpg)
Could check [saml-single-sign-on.html](http://www.zohocorp.com.cn/manageengine/products/passwordmanagerpro/help/saml-single-sign-on.html)For more details.

### Audit Logs
Individual and enterprise client, especially enterprise client, they design to fully control the security and access information within their organization. Audit log helps them meet this requirement.
The best audit log need have:

+ Actor
+ Group
+ Where
+ When
+ Target
+ Action
+ Action Type
+ Event Name
+ Description

Good to have:
+ Server
+ Version
+ Global Actor Id

Audit log should be immutable, searchable, filterable and exportable. 
It's good to be API accessible and have clear document for lag time and retention time.  
ie. [Viostream Help
](https://help.viostream.com/hc/en-us/articles/115012650048-Viostream-7-4-0-release-Advanced-audit-log),
![Viostream Help]({{ site.url }}/images/enterprise_ready/audit_log/audit_log1.png)
[confluence
](https://confluence.atlassian.com/doc/audit-log-829076528.html)
![confluence]({{ site.url }}/images/enterprise_ready/audit_log/audit_log2.png)


### RBAC
Usually, for enterprise client, they have requirements that fully control the access of the team members to make sure they meet the least privilege principle.
It's not possible to assign access to each team member one by one. At this point, SaaS application need "role". 
The relation map normally like "user->role->access->operation/resource"
![RBAC]({{ site.url }}/images/enterprise_ready/rbac/rbac1.png)
Good end experience needed once client access been assigned. ie. if user do not have any access of a operation, ensure the button is disabled. It's good to have clear
message for the user.
ie. AWS console may need some optimizations. User without aws api gateway any permission still could click the button, although user get a alert message.
![RBAC]({{ site.url }}/images/enterprise_ready/rbac/rbac2.png)


### Change Management
A clear release and change log is needed. If some important or big change happened, it's good to have some advance communication, like put a obviously alert on the page, 
send a official email, add beta or lab icons. 
In account level, the change management need to support customer environment when there are some upcoming client side release need to integrate with SaaS application. User could do testing 
in sandbox environment and do it in same way once they are satisfied with the change.
ie. salesforce provide the sandbox feature to enterprise client. By just a simple click, user could create a isolated sandbox environment from their production org. The change in sandbox would not 
effect the pro environment.
[https://help.salesforce.com/articleView?id=create_test_instance.htm&type=5](https://help.salesforce.com/articleView?id=create_test_instance.htm&type=5)
![salesforce]({{ site.url }}/images/enterprise_ready/change_management/change_management1.png)

### Product Security
Recently, Facebook meet "some" problem about the data leakage. After two days congressional hearing, the situation seems to be stable till now. 
![product_security_1]({{ site.url }}/images/enterprise_ready/product_security/product_security1.png)
![product_security_2]({{ site.url }}/images/enterprise_ready/product_security/product_security2.png)
It's tell us security is not just a word or a separated topic but around every thing on enterprise SaaS application. The description below copy from [https://www.enterpriseready.io/features/product-security/](https://www.enterpriseready.io/features/product-security/), it give the security details of every part of SaaS product.


    Security isn’t a topic to discuss by itself, it should be part of the conversation around everything discussed on EnterpriseReady. For SaaS companies working with larger enterprise IT buyers it is incredibly important for this to be an area of strength. Most enterprises will provide potential software vendors with long third-party vendor security questionnaires. These documents generally cover a broad range of security topics including product security, physical office security, employee screening, office network security, business continuity, upstream vendor security, disaster recovery, data security, secure IT policy, asset security and data center security in order to evaluate the overall security posture of an organization.
    
    For the purpose of this guide we’re going to focus on product related security issues. These be broken into 3 primary categories of development: security, ops security and product security. We also recommend a detailed review of the security controls recommended by OWASP.
    
    Ops
    Communication Security
    TLS all the things (including internal server-to-server communication). It’s just not acceptable to use unencrypted communication inside your datacenter.
    
    Data Security
    Encryption, least privilege, deletion, auditing use of privileges. When possible (which should be all of the time), leverage systems to encrypt data while at rest. Store PII (Personally Identifiable Information) in separate systems. Be aware of any compliance and regulatory processes that are specific to your industry and be prepared to explain how you follow them.
    
    Network Segmentation
    Create private networks by leveraging cloud hosting features such as VPCs and VPN connections when possible. Don’t treat your office location as an extension of this network. Anyone who needs to access the production network should have a VPN connection from their own device.
    
    Public Footprint
    The principle of least privilege can be applied to the openness of systems and servers. By doing so you can reduce the surface area for potential attacks. Servers should not have public IP addresses unless absolutely needed (instead traffic should be routed through a reverse proxy server like Nginx). All unnecessary ports should be blocked. Only allow SSH connections from internal (VPN) connections. Moving the SSH port to a different port isn’t a secure solution.
    
    Take Preventative Action
    There are some external products and services that can help detect when your network has been compromised or if your applications have known vulnerabilities. Some can even assist you to deploy patches or block access to bad actors. A examples of products to evaluate are: SignalSciences, AlienVault, Snort or Tenable.
    
    DDOS Prevention
    If you’ve prevented rogue traffic from accessing your servers and network, it’s still possible for external services to block anyone else from using your service by creating a distributed, denial-of-service attack. To prevent this, it’s recommended to use a 3rd party service such as Cloudflare or Akamai who have experience managing DDOS attacks.
    
    API Rate Limiting Without rate limiting, your API is subject to brute force attacks. Consider enforcing rate limits in an upstream load balancer. It’s also recommended that unauthenticated requests have a considerably lower rate limit than authenticated requests.
    
    Monitoring
    All of the best security measures in place aren’t enough if you don’t have monitoring in place to know when you’ve been compromised. Invest in detailed monitoring that will alert you when unexpected events occur such as SSH connections from new IP addresses, high network throughput, high CPU, new processes running on servers, etc.
    
    Automation
    Reproducibility is key to ensuring consistent and intended environments by removing opportunity for human error. Without scripted and reproducible deployments, you will end up with a collection of unaudited, artisanally configured servers that could have undetected security vulnerabilities on them.
    
    Enable and Enforce Two Factor Auth
    For any system that support two-factor authentication, you should require that your own employees and contractors enable this feature. Many products even have a way to enforce it for all members of your team.
    
    Separation of Roles
    Most large enterprises will require that you have different roles for development, ops, monitoring, etc. It’s important to be able to provide this, but there isn’t a requirement that a single person cannot be in multiple roles. It should be structured so that a developer who also has SSH access to production servers cannot be assuming both roles simultaneously though.
    
    Leverage Trusted Images
    If using Docker, enable Content Trust to ensure the image lifecycle is securely managed.
    
    Development
    Feature Security Design Reviews
    All changes that have a design review should focus on a security review also. If you aren’t an expert in security, bring an expert in who can show you where the weak points are and design a way to harden these before writing the new system.
    
    Product Architecture
    Having a current product architecture document is a prerequisite for being able to think about product security. It’s important to keep your product architecture documents current.
    
    Dev Environments
    Your development environments should be isolated from production and staging systems. Each development environment should not be able to access production databases and resources. Any secrets that are required for your environment should be different in dev than in production or staging.
    
    Encryption Primitives
    Ensure that encryption methods are not “homegrown”. Use industry defined standards when encrypting data. Also, it’s important to know your (potential) buyer. Common standards like RSA and AES are frequently accepted. Some encryption libraries require initialization with a securely seeded random number generator, be sure to read the docs or code for your uuid library to determine if you are responsible for seeding.
    
    Code Reviews
    Require that all code is reviewed by a separate person than the author. This will help eliminate rogue actors from introducing intentional or unintentional security bugs into your system.
    
    Static Code Analysis
    When possible, leverage automation tools such as Checkmarx, Veracode and others to inspect code changes for security vulnerabilities.
    
    Test Dependencies
    While it’s common to think about vulnerabilities in your own code, most software today has a lot of dependencies. You should remember to check for vulnerabilities in all of your dependencies also. Depending on the language you are using, tools like Snyk are available to help automatically monitor and scan these.
    
    Secret Management
    Your code should use a vault to store secrets when possible. Consider using Hashicorp Vault or Torus.sh for this.
    
    Application Security
    Read the OWASP Top 10 Vulnerabilities, near the bottom of that page, labeled A1-A10, and think about how someone could use these vulnerabilities to gain access to your system, databases or network
    
    Continuous Threat Modeling
    Identify and document your trust boundaries and threat models in your system early. Continue to update these docs as your system evolves. Knowing where the trust boundaries are and where the biggest threat could come from will help you prioritize solutions.
    
    Product
    Password security
    Many applications require that users sign up and will have to store passwords to allow the user to log in again. You will have to be able to explain the following features around password security:
    - How are passwords stored? (bcrypt is a great answer, but make sure it’s including a salt.)
    - Do you enforce a minimum password strength?
    - Do you have a configurable password expiration policy?
    - Do you prevent password reuse when changing a password?
    - How can a password be reset when needed?
    - Can a user enable a second factor login requirement?
    - Can an organization enforce 2 factor login for all users?
    
    API Tokens
    API Tokens are often treated less securely than passwords, but they are just as powerful as a logged in user. Treat tokens with the same level of security as you would passwords.
    
    IP Whitelisting Restrict the users of an account to only be able to log in from specified IP addresses or ranges (useful for enterprises that require that their users be on a VPN to use any service).
    
    Sessions
    When an account is deleted or has a password changed, all existing sessions should be deleted.
    
    Client side
    If your application is using cookies or local/session storage, ensure that all confidential data stored is in server-readable JWT tokens only.
    
    Secure defaults
    Security shouldn’t be a feature a user has to enable or opt into. Enforce secure defaults out of the box.
    
    Demonstrable security
    Many enterprise buyers will ask you to provide documentation around your security efforts. Be prepared to show any of the following:
    Certifications - Achieving SOC2 certification or FedRAMP or PCI compliance (even when irrelevant) can often shortcut the effort to demonstrate security practices to potential customers.
    Audits - Trusted third party audits are a standard requirement for most enterprise customers.
    Penetration Test Results
    Security Forms - Many potential customers will require that you fill out their detailed security check list.
    White Papers
    
    Information Security Policy
    Have a written and published Information Security Policy, Incident Response Plan and Bug Bounty Program. Some good examples to start with are Datadog or Dropbox. It’s pretty common to have these hosted on a /security URL.

This is my own personal experience in 2016, my company get a email from wooyun, a website of white hat group of China, which is out of service right now. The email mentioned that our SaaS product have several vulnerabilities, the report still could find in
[http://www.anquan.us/static/bugs/wooyun-2016-0165405.html](http://www.anquan.us/static/bugs/wooyun-2016-0165405.html).  

  
### Deployment Options
For enterprise user, especially chemical, military and government user, usually, they will have their own account system, additionally, their network is isolated from public, hence SaaS application provider
    have to consider that split the application into cloud version and self-hosted version.
Installation should be design as simple as possible. This need provider prepare a clear documentation, contains server requirement, system requirement, software requirement, network requirement(like which port should be open)
It's best to put the application into a single docker image or single vm, that will make things much simpler and professional.
As mentioned above, the network is usually isolated, hence the upgrade strategy, audit log ,monitoring console, HA, FT, auto scaling assurance is important. Leverage a file path to auto detective for upgrade is a good choice. In this way, you 
can send your upgrade file by hard disk, cd etc... then client could simply copy them and upgrade the application. 
Don't forget to backup user data on a regular basis, this helps you reduce the lost once the application need to be recovered.
ie. JIRA provide a good example for self-hosted option.
[https://confluence.atlassian.com/adminjiraserver/installing-jira-applications-on-linux-938846841.html](https://confluence.atlassian.com/adminjiraserver/installing-jira-applications-on-linux-938846841.html)
 ![JIRA Self-hosted]({{ site.url }}/images/enterprise_ready/deployment_options/JIRA Self-hosted1.png)
 ![JIRA Self-hosted]({{ site.url }}/images/enterprise_ready/deployment_options/JIRA Self-hosted.png)
 
    
### Team Management
It's unsafty and inconvenient that enterprise client share one account to do their job. Multi-tenancy is required to resolve this problem.
The enterprise client could create or invite college to create accounts within their organization. This kinds of user could reach the resource which they have permissions.
Additionally, they could create their own workspace and add private resource, and share them with others if they want. Live revisions if important when multi accounts modify same resource. So with same reason, resource versioning also important.
 ![JIRA Self-hosted]({{ site.url }}/images/enterprise_ready/team_management/team_management1.png)
 ![JIRA Self-hosted]({{ site.url }}/images/enterprise_ready/team_management/team_management2.png)
 


### Integrations
There are two parts of "Integration". One is for basic API and data export function, use could use the api or export function to get the data they want and integrate with their other application.
ie. JIRA RESTful API
 [https://developer.atlassian.com/server/jira/platform/rest-apis/](https://developer.atlassian.com/server/jira/platform/rest-apis/)
One is for "Admin control integration", this need SaaS application have a ease to use console for enterprise administrator to do the configurations. Usually, SaaS application will support integrate with some popular applications. 

### Reporting & Analytics
Security and Analytics think to be the most two important functions in Enterprise SaaS products. It's let your application differentiate from others.
Below content copies from [https://www.enterpriseready.io/features/advanced-reporting/](https://www.enterpriseready.io/features/advanced-reporting/)

    Reporting and analytics embedded into enterprise applications can differentiate products and are 
    expected by many enterprise buyers. The more people using your application, the more
    functionality in reporting they will want. Consider a simple CRM app - if you sell it to
    small sales teams then only the sales department will want a couple of simple pipeline reports.
    If you sold it to an enterprise you’ll have different tiers of sales managers, lead developers, 
     marketing, finance, support staff and many more. Each of these groups expects different answers.
    A recent survey by Aberdeen Group, shows that 73% of ISVs include reporting and analytics in 
    their software as a basis to differentiate their products and develop competitive advantage. 
    Other reasons include improving end-user experience and increasing average customer value.
    If system logs are for developers and audit logs are for admin record keeping then reporting
    is for business. In other words, data related to the business purpose of the application, 
    not the nuts and bolts. 
    Advanced reporting is an important feature for enterprise buyers because it allows them
     (or the administrator) to prove that the software in itself was effective (justifying the 
    price they paid). It isn’t enough for software to be effective, it has to be provably
     effective. The more ways you enable customers to slice and manipulate the data that your
    application produces, the more likely that they’ll be able to prove effectiveness and
    derive value from your apps.
    Reporting and analytics can also affect the bottom line, SaaS companies can charge 8x more per
    user for an edition that includes advanced analytics, compared to basic reporting. The better 
    the analytics in your app, the better the insights that people glean from them and the better 
     valued the tool.

As the content mentioned above, every department of the enterprise have their own goal and requirement, it's hard to provide all charts they want.
Hence, data export, RESTful API and especially self-Authoring and analytics is needed besides the canned interactive reporting and dashboards.
  ![reporting_analytics]({{ site.url }}/images/enterprise_ready/reporting_analytics/reporting_analytics1.png)
In book "[Embedding Analytics in Modern Applications](https://www.oreilly.com/data/free/embedding-analytics-in-modern-applications.csp)", it shows how to provide distraction-free insights to end users.It's mentioned that end users don’t want to use a “BI tool” — another interface to learn, 
another login—they want easily accessible answers. Instead of offering a standalone dashboard, the new trend is to embed analytics into applications that are already used every day.
Embedding analytics in a familiar application allows for a streamlined UI, leading to wider adoption and product use (“stickiness”).
This increases the value of your product through customer retention and a competitive differentiation that leads to more new customer growth.  
If you’ve already invested resources in analytics, these drivers could indicate that you’d benefit from an embedded solution:  

+ Your focus groups report that users value the analytics in your application  
+ You have an opportunity to monetize the data captured by your application  
+ You want to offer more sophisticated analytics, or your customers are reporting some dissatisfaction with the current analytics reporting your product provides
+ You lack sufficient ad hoc or self-service capabilities, resulting in too much development time providing custom reports or queries  
+ Your competitors’ reporting is superior (and you are losing customers as a result)  
+ You are planning a migration to SaaS and are not sure your current analytics solution will meet your needs for a multitenant environment  

A recent survey by the Aberdeen Group reported that 73% of independent software vendors (ISVs) have product differentiation as  their primary objective for embedding analytics within their application
 ![reporting_analytics]({{ site.url }}/images/enterprise_ready/reporting_analytics/reporting_analytics2.png)

There are several interfaces and methods for embedded analytics:

+ Static Data  
  This interface provides your users with a simple snapshot in time. The report can be downloaded (typically as a Microsoft Excel worksheet or a print-ready PDF) and can be designed for high-volume use. The end user is typically only allowed to make changes to date ranges and select a downloadable format. Any changes to the report (or new report requests) must be built by your developers.
  + Method: REST APIs or reporting libraries  
  
+ Interactive Data
  An interactive data experience allows users more flexibility in modifying reports to suit their needs; they can apply filters or select different report types. This allows them to identify trends and easily flag outliers (features that are not possible with a static interface). This dashboard approach is a common way to provide a more customized user experience inside structured reports.  
  + Method: BI tools offering iFrames (analytics hosted in a separate tab or page) or custom development
  
+ Self-Service Exploration
  A self-service tool allows users more flexibility to manipulate data in an easy-to-understand format. Instead of a raw data dump, users can select clearly named data sets to create their own graphics. A truly exploratory experience allows users to aggregate data across multiple dimensions and analyze using drill down, slice and dice, or pivot capabilities.
  + Method: Use an API-based BI tool, use a BI scripting framework, or custom development

Build or Buy?  
Once you’ve painstakingly designed, built, and optimized your custom application, it can be hard to imagine an out-of-the-box analytics product meeting all your needs.
![reporting_analytics]({{ site.url }}/images/enterprise_ready/reporting_analytics/reporting_analytics3.png)
![reporting_analytics]({{ site.url }}/images/enterprise_ready/reporting_analytics/reporting_analytics4.png)
The obvious concern with buying a solution is whether the product will meet your business needs, including any custom reporting. Additional challenges are whether you can customize the design to match the look and feel of your application, and whether the overhead of an analytics tool will negatively affect product performance.  

Choosing the Right Tool: Seven Challenges and their Best-Practice Solutions:

+ *Customization* 
    + Will it look like the rest of my application? 
    + Can I easily customize it?
+ *Usability*  
    + Will it please my customers? 
    + Will it provide a seamless experience between the BI and my application?
+ *Capabilities*  
    + Can it meet my business needs?
+ *Multitenancy*  
    + Can it support the security and access permissions my product needs?  
        If a vendor only offers REST APIs or iFrames, you should consider whether these methods are flexible enough to meet your needs as your product (and your users’ expectations) evolve.
+ *Scalability*  
    Can it scale with my application?
+ *Data Structure*  
    + Will it work for my data structure/support my data streams?  
    You may have entirely SaaS data sources or only use open source SQL databases. You may have an existing data warehouse or use OLAP cubes, or you may only utilize next-gen MPP databases like Redshift or Vertica.
+ *Performance*  
    + Will it slow down my application?

EnterpriseReady.io give us three excellent examples  
[https://www.enterpriseready.io/github/reporting/](https://www.enterpriseready.io/github/reporting/)  
[https://www.enterpriseready.io/hubspot/reporting/](https://www.enterpriseready.io/hubspot/reporting/)  
[https://www.enterpriseready.io/fbworkplace/reporting/](https://www.enterpriseready.io/fbworkplace/reporting/)

### SLA Support
For (S)ervice (L)evel (A)greement, SaaS application provider need to give a transparent uptime and response time promise.
  This could make client trust you have ability to maintain the application well. It's good to have a clear real time status page.
  ![SLA1]({{ site.url }}/images/enterprise_ready/sla/sla1.png)
  ![SLA2]({{ site.url }}/images/enterprise_ready/sla/sla2.png)
  ![SLA3]({{ site.url }}/images/enterprise_ready/sla/sla3.png)
   ![SLA4]({{ site.url }}/images/enterprise_ready/sla/sla4.png) 
   
Additionally, customer tickets maintain strategy(phone call/ email, etc...), transparent road map, regular training also important for SLA support. 
![SLA5]({{ site.url }}/images/enterprise_ready/sla/sla5.gif)

### GDPR
![gdpr]({{ site.url }}/images/enterprise_ready/gdpr/gdpr.png)
(G)eneral (D)ata (P)rotection (R)egulation rise by European Union and will take effect in May 25, 2018.
If you run business in European, you should care about it. 
There are 3 key points:
    
+ Deletion Tool
 
User could delete all their data once they decide delete their account or decide to clean their account history. 

+ Exportable

User could export all their data in a readable format.

+ Data Isolation

European Union citizen user information only can store in European Union region. SaaS application company should consider to change their data synchronization strategy and service deployment architecture.


Reference:  
[https://www.enterpriseready.io/](https://www.enterpriseready.io/)
[http://www.woshipm.com/operate/892776.html](http://www.woshipm.com/operate/892776.html)
[https://status.twilio.com/#month](https://status.twilio.com/#month)
[https://www.enterpriseready.io/hubspot/reporting/](https://www.enterpriseready.io/hubspot/reporting/)
[https://www.harrisonmetal.com/library/pricing-3-assortments-are-for-winners](https://www.harrisonmetal.com/library/pricing-3-assortments-are-for-winners)
[https://www.zhihu.com/question/20313385](https://www.zhihu.com/question/20313385)
[http://www.chanpin100.com/article/105917](http://www.chanpin100.com/article/105917)
[https://www.oreilly.com/data/free/embedding-analytics-in-modern-applications.csp](https://www.oreilly.com/data/free/embedding-analytics-in-modern-applications.csp)


