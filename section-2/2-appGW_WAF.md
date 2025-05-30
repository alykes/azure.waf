# What is Azure Web Application Firewall on Azure Application Gateway?

The Azure Web Application Firewall (WAF) on Azure Application Gateway actively safeguards your web applications against common exploits and vulnerabilities. Web applications are frequent targets for malicious attacks, these attacks often exploit well-known vulnerabilities such as SQL injection and cross-site scripting.

WAF on Application Gateway is based on the [Core Rule Set (CRS)](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules) from the Open Web Application Security Project (OWASP).

All of the following WAF features exist inside of a WAF policy. You can create **multiple** policies and associate them with:  

- an Application Gateway
- to individual listeners
- or path-based routing rules on an Application Gateway

This enables you to define **separate policies** for **each site** behind your Application Gateway.

> Application Gateway has two versions of the WAF SKU: Application Gateway WAF_v1 and Application Gateway WAF_v2. WAF policy associations are only supported for the Application Gateway WAF_v2 SKU.

![Image](./appGW_WAF.png)

## Benefits

This section describes the core benefits that WAF on Application Gateway provides.

## Protection

- Protect your web applications from web vulnerabilities and attacks without modification to back-end code
- Protect multiple web applications at the same time. An instance of Application Gateway can host up to 40 websites that are protected by a web application firewall
- Create custom WAF policies for different sites behind the same WAF
- Protect your web applications from malicious bots with the IP Reputation ruleset.
- Protect your application against DDoS attacks

## Monitoring

Monitor attacks against your web applications by using a real-time WAF log. The log is integrated with Azure Monitor to track WAF alerts and easily monitor trends.

The Application Gateway WAF is integrated with Microsoft Defender for Cloud. Defender for Cloud provides a central view of the security state of all your Azure, hybrid, and multi-cloud resources.

## Customisation

- Customize WAF rules and rule groups to suit your application requirements and eliminate false positives
- Associate a WAF Policy for each site behind your WAF to allow for site-specific configuration
- Create custom rules to suit the needs of your application

## Features

- SQL injection protection
- Cross-site scripting protection
- Protection against other common web attacks, such as command injection, HTTP request smuggling, HTTP response splitting, and remote file inclusion
- Protection against HTTP protocol violations
- Protection against HTTP protocol anomalies, such as missing host user-agent and accept headers
- Protection against crawlers and scanners
- Detection of common application misconfigurations (for example, Apache and IIS)
- Configurable request size limits with lower and upper bounds
- Exclusion lists let you omit certain request attributes from a WAF evaluation. A common example is Active Directory-inserted tokens that are used for authentication or password fields
- Create custom rules to suit the specific needs of your applications
- Geo-filter traffic to allow or block certain countries/regions from gaining access to your applications
- Protect your applications from bots with the bot mitigation ruleset
- Inspect JSON and XML in the request body

## WAF policy and rules

To enable a Web Application Firewall on Application Gateway, you must **create a WAF policy**.

This policy is where all of the:

- managed rules
- custom rules
- exclusions
- and other customisations such as file upload limit exist.

You can configure a WAF policy and associate that policy to one or more application gateways for protection.

A WAF policy consists of two types of security rules:

- Custom rules that you create
- Managed rule sets that are a collection of Azure-managed preconfigured set of rules

When both are present, **custom rules are processed before processing the rules in a managed rule set**.

A rule is made of:

- a match condition
- a priority
- an action (Action types supported are: ALLOW, BLOCK, and LOG)

 You can create a fully customized policy that meets your specific application protection requirements by combining managed and custom rules.

## Rule Processing Priorities

Rules within a policy are processed in a priority order. **Priority** is a unique integer that defines the order of rules to process. **Smaller integer value denotes a higher priority** and those rules are evaluated before rules with a higher integer value.  
Once a rule is matched, the corresponding action that was defined in the rule is applied to the request. Once such a match is processed, rules with lower priorities aren't processed further.

## WAF Associations

A web application delivered by Application Gateway can have a WAF policy associated to it at:

- the `global` level
- at a `per-site` level
- or at a `per-URI` level

## Core rule sets

Application Gateway supports multiple rule sets, including `CRS 3.2`, `CRS 3.1`, and `CRS 3.0`. These rules protect your web applications from malicious activity. For more information, check out [Web Application Firewall DRS and CRS rule groups and rules.](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules)

## Custom rules

Application Gateway also supports custom rules.  

With custom rules, you can create your own rules, which are evaluated for each request that passes through WAF.

> These rules hold a higher priority than the rest of the rules in the managed rule sets.

If a set of conditions is met, an action is taken to **allow** or **block**. For more information on custom rules, see [Custom rules for Application Gateway.](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/custom-waf-rules-overview)

There is a geo-match operator available for custom rules. See [Geo-match custom rules](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/custom-waf-rules-overview#geomatch-custom-rules) for more information.

## Bot protection rule set

You can enable a managed bot protection rule set to take custom actions on requests from all bot categories.

Three bot categories are supported:

- **Bad**

Bad bots are bots with:

    - malicious IP addresses
    - bots that have falsified their identities.

Bad bots include malicious IP addresses that are sourced from the **Microsoft Threat Intelligence** feed’s **high confidence** IP Indicators of Compromise and IP reputation feeds. Bad bots also include bots that identify themselves as good bots but their _IP addresses don’t belong to legitimate bot publishers_.

- **Good**

Good Bots are trusted user agents. Good bot rules are categorized into multiple categories to provide granular control over WAF policy configuration. These categories include:

    - verified search engine bots (such as Googlebot and Bingbot)
    - validated link checker bots
    - verified social media bots (such as Facebookbot and LinkedInBot)
    - verified advertising bots
    - verified content checker bots
    - validated miscellaneous bots

- **Unknown**

Unknown bots are user agents without additional validation. Unknown bots also include malicious IP addresses that are sourced from **Microsoft Threat Intelligence** feed’s **medium confidence** IP Indicators of Compromise.

The WAF platform actively manages and dynamically updates the bot signatures.

When Bot protection is enabled, it **blocks**, **allows**, or **logs** incoming requests that match bot rules based on the configured action.  
It blocks malicious bots, allows verified search engine crawlers, blocks unknown search engine crawlers, and logs unknown bots by default.  
You can set custom actions of different types of bots to:

    - block
    - allow
    - log

You can access WAF logs from a storage account, event hub, log analytics, or send logs to a partner solution.

For more information about Application Gateway bot protection, check out [Web Application Firewall on Application Gateway bot protection.](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/bot-protection-overview)

## WAF modes

- `Detection mode`: Monitors and logs all threat alerts. You turn on logging diagnostics for Application Gateway in the _Diagnostics section_. You must also _make sure that the WAF log is selected and turned on_.
  > Web application firewall doesn't block incoming requests when it's operating in Detection mode.
- `Prevention mode`: Blocks intrusions and attacks that the rules detect. The attacker receives a "403 unauthorized access" exception, and the connection is closed. Prevention mode records such attacks in the WAF logs.
  