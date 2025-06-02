# Azure API Management & Serverless Integration Services Track

Reference: [Udemy Course](https://www.udemy.com/course/the-ultimate-azure-api-management-course/learn/lecture/39330078#overview)

## Web Application Firewall

Introduction to the WAF set up on Azure FrontDoor. We'll go through the following:

- Managed Rules
- Custom Rules
- Policies
- WAF modes

## How does WAF work?

WAF protects the web application and web services against common attacks.  

**Where does the WAF live? Where is it deployed?**  
We know that Azure FrontDoor is a global service, therefore you will get an Azure FrontDoor instance in every location. The WAF is deployed at the edge locations, closer to the attacker. This ensures that performance isn't compromised.

## WAF Policies

You have managed rules (microsoft rules) and custom rules (user defined). Custom rules are evaluated before the managed rules. You can set up either of the two WAF policies:

- Global WAF (**FrontDoor**)
- Regional WAF (**Application Gateway**)

## WAF Modes

To switch the WAF from detection to prevention, you do this on the Overview of the WAF Policy. Remember that detection only logs the events, whereas prevention blocks the rules or performs whatever action you specify.

## Managed Rules Selection - FrontDoor

> Managed rules are only supported for FrontDoor Premium and FrontDoor Classic  

In the WAF Policy that you are configuring, you can set the `Default rule set` under the `Managed rules` section, clicking the drop down and selecting one of:

- Microsoft_DefaultRuleSet_1.1
- Microsoft_DefaultRuleSet_2.0
- Microsoft_DefaultRuleSet_2.1
- DefaultRuleSet_1.0
- DefaultRuleSet_preview-0.1
- None

The additional rules for Bot Protection are:

- Microsoft_BotManagerRuleSet_1.0
- Microsoft_BotManagerRuleSet_1.1
- BotProtection_preview-0.1
- None

## Managed Rules Selection - Application Gateway

The managed rules for the application Gateway are slightly different to FrontDoor, you can only select from the following list of managed rules:

- Microsoft_DefaultRuleSet_2.1
- OWASP_3.1
- OWASP_3.2

The additional rules for Bot Protection are:

- Microsoft_BotManagerRuleSet_0.1
- Microsoft_BotManagerRuleSet_1.0
- Microsoft_BotManagerRuleSet_1.1
- None

## WAF Logs - FrontDoor

To set up diagnostic logs for your FrontDoor, so that you are able to search through **FrontDoorAccessLog** and **FrontDoorWebApplicationFirewallLog**, you will need to go to `Diagnostic Settings`, then `+ Add diagnostic settings`. You will also require a Log Analytics Workspace, so ensure that you set one up, so that the logs can be sent there.

## Managed Rules

In the list of Managed Rules, you can select individual rules and either `Enable` or `Disable` them. This is a simple method to manage the rules. Ensure that when you `Disable` the rule that you have a very specific reason as to why you are doing that and what that means to the traffic hitting the WAF.

Instead of setting a rule to disabled, remember that you can also set different actions for a specific rule. The options are:

- Allow
- Log
- Redirect
- Block

### Managed Rule Exclusions

You can also add **managed exclusions** to for specific cases that you want to allow through the WAF.  

#### FrontDoor Exclusions

> Managed exclusions can be applied to a **Rule Set**, **Rule Group** or a specific **Rule**!
> You can't add exclusions to the BotManagerRuleSet

To add a managed exclusion:

- Click on the Managed Exclusions link
- Click `+ Add`, to add an exclusion
- Select the **Rule Set**, **Rule Group** and the specific **Rule**
- Select the **Match variable**
  - Request header name
  - Request cookie name
  - Query string args name
  - Request body post args name
  - Request body JSON args name
- Select the **Operator**
  - starts with
  - ends with
  - Equals
  - Contains
  - Equals any
- Finally select the **Selector** (the value you would like to find with this rule)

#### Application Gateway Exclusions

> Managed Exclusions always need to apply to a specific rule, whether it is **Global**, the **OWASP/DRS set** or a **bot rule set**.

To add a managed exclusion:

- Click on the `+ Add exclusions` link
- Select the RuleSet that you want to apply the rule to
- Click `+ Add rules` to select the specific rule to apply the exclusion to
- Select the Match variable
  - Request Header Keys
  - Request Header Names
  - Request Header Values
  - Request Cookie Keys
  - Request Cookie Names
  - Request Cookie Values
  - Request Arg Keys
  - Request Arg Names
  - Request Arg Values
- Select the Operator
  - starts with
  - ends with
  - Equals
  - Contains
  - Equals any
- Finally select the **Selector** (the value you would like to find with this rule)

The exclusion will now show up in the **Exclusions** link under **Managed rules**

## Custom Rules

> Custom rules are those set up by an end user. These rules are evaluated before the managed rule sets.

You need to set up a priority for each rule, they lower the priority the higher the priority, eg 1 is the highest priority. Custom Rules are handled quite similarly between App Gateway and FrontDoor with minor variations. The following are the rule types that you can create.

**FrontDoor**  match types:

- `Geo location` - (RemoteAddr or SocketAddr)
- `IP address` - (RemoteAddr or SocketAddr)
- `Size` - (QueryString, RequestUri, RequestHeader, PostArgs, RequestBody, Cookies)
- `String` - (RequestMethod, QueryString, RequestUri, RequestHeader, PostArgs, RequestBody, Cookies)

**App Gateway** match types:

- `IP address` - (RemoteAddr)
- `Number` - (RequestMethod, QueryString, RequestUri, RequestHeaders, PostArgs, RequestBody, RequestCookies)
- `String` - (RequestMethod, QueryString, RequestUri, RequestHeaders, PostArgs, RequestBody, RequestCookies)
- `Geo location` - (RemoteAddr, RequestHeaders) -> you can then select the country/region

For each of these rules, on both the FrontDoor and Application Gateway, you can either select to `Match` or `Rate Limit`. You can add multiple conditions to a custom rule. After selecting a match type, you are then given additional criteria to match it with, as well as context appropriate **operators**.

## WAF Settings

The WAF Settings differ between Azure FrontDoor and Azure Application Gateway.

### WAF Settings - FrontDoor

WAF Status: Disable and Enable  
WAF Modes: Detection Mode and Prevention Mode  

Policy Settings:  

- Enable request body inspection
- Redirect URL
  > You can only have one redirect URL per policy
- Block response status code - (200,403,405,406,429,990,991,992,993,994,995,996,997,998,999 are the only supported status codes)
- Block response body
- Add a custom response message when a request is blocked by a WAF rule.
- JavaScript Challenge expiration (mins) (preview) [default: 30]
- CAPTCHA expiration (mins) (preview) [default: 30]

### WAF Settings - Application Gateway

WAF Status: Disable and Enable  
WAF Modes: Detection Mode and Prevention Mode  

Policy Settings:

- Enforce request body inspection
  - Maximum request body inspection limit (KB) [default: 128]
- Enforce maximum request body limit
  - Maximum request body size (KB) [default: 128]
- Enforce maximum file upload limit
- Maximum file upload size (MB) [default: 100]
- JavaScript Challenge action settings
  - JavaScript Challenge expiration (mins) (preview) [default: 30]

## Associations

You can associate one WAF policy to many Azure FrontDoors but Azure FrontDoor can only have one WAF Policy assigned to it at a time.

## Log Query

To query the logs, click on the **logs** link under **Monitoring** (in either the FrontDoor or Application Gateway). Click on the Firewall Audit query, this will run a query against the logs.  

An example query follows:  

```sql
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "FrontDoorWebApplicationFirewallLog"
```
