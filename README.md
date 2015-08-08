# thirdman
Third Man Correlation Search

The thirdman correlation search detects misappropriated credentials using an abstract statistical fingerprint of users' successful auth behaviour.

——-

### OVERVIEW

- About the thirdman
- Release notes
- Performance benchmarks
- Support and resources

### INSTALLATION

- Hardware and software requirements
- Installation steps
- Deploy to single server instance
- Deploy to distributed deployment with Search Head Pooling
- Deploy to distributed deployment with Search Head Clustering
- Deploy to Splunk Cloud 

### USER GUIDE

- Key concepts
- Data types
- Lookups
- Configure thirdman
- Troubleshooting
- Upgrade
- Example Use Case-based Scenario

---

### OVERVIEW

#### About the thirdman

| Author | Doug Brown |
| --- | --- |
| App Version | 1.0.0 |
| Vendor Products | Optional: Splunk Enterprise Security 3.2.2 |
| Has index-time operations | false |
| Create an index | false |
| Implements summarization | ES correlation search creates events in 'notable' summary index |

##### Inspiration

The use of stolen credentials, via phishing or other means, is becoming one of the greatest challenges security professionals face. Based on the notion that people's behaviour form predictable patterns, I thought it must be possible to "profile" users' successful auth and therefore detect the use of misappropriated credentials. Like all good ideas, the mechanism of this correlation search came to me as I was trying to get to sleep one night and was consequently developed on the back of an envelope.

Regarding the name of this entry: At the time of submission to this competition, I needed to quickly come up with a name to succinctly describe this correlation search, but "Stolen Credentials" or similar, struck me as too mundane. The Third Man (an obvious movie reference) sounds better, and initially was just an allusion to a bad actor. However, after more thought, if we consider the first man to be the administrator, the second man to be the user, and the third man to be the bad actor, I think this analogy works quite well.

##### How it works

This correlation search takes the CIM Authentication data model and enriches it with autonomous system information and an abstraction of time, then creates a statistical "fingerprint" of each users' behaviour in relation to what, when, where and how they successfully auth. A significant deviation from a user's pattern triggers the alert. Although this sounds relatively straightforward, importantly this correlation searches' ability to detect anomalous behaviour is derived from it's unique high-level abstraction of circumstances.

Most alerts of a similar nature use geographical topology lookups (known for being less than reliable), however this alert uses a logical network topology lookup to determine where a user authenticates from in relation to the structure of the Internet. This is significant because a typical user accesses the Internet from half a dozen or less Autonomous Systems (akin to postcodes at a global scale), in their day-to-day activities. By using statistics to model the likelihood of a user authenticating from a particular part of the Internet in relation to the other aspects of the tuple (what the resource is that's being accessed, when it's being accessed, and how it's being accessed), we can make a determination as to how confident we can be that the user credentials being used and the person they represent are one and the same.

##### Scripts and binaries

None.

#### Release notes

##### About this release

Version 1.0.0 of the thirdman is compatible with:

| Splunk Enterprise versions | 6.2+ |
| --- | --- |
| CIM | 4.2 |
| Platforms | Platform independent |
| Vendor Products | Optional: Splunk Enterprise Security 3.2.2 |
| Lookup file changes | None |

##### New features

The thirdman includes the following new features:

- Added Third Man Alert Template for those without the Splunk app for Enterprise Security

##### Fixed issues

Version 1.0.0 of the thirdman fixes the following issues:

- None

##### Known issues

Version 1.0.0 of the thirdman has the following known issues:

- It appears that 'import' directive in ES' metadata may need to be modified to include the 'thirdman' app for the correlation search to appear in the ES 'Custom Searches' GUI.

##### Third-party software attributions

Version 1.0.0 of the thirdman incorporates the following third-party software or libraries.

- None

#### Performance benchmarks

This app was developed using "The Audit Experiment" honeypot (https://github.com/doksu/splunk_auditd/wiki/The-Audit-Experiment). As with any app of this nature, it can require significant resources.

##### Support and resources

**Questions and answers**

Please post questions to: answers.splunk.com

**Support**

This app is provided as is with no warranty, implied or otherwise; please see the LICENSE document for more information. Feedback about possible improvements and good news stories of how this app has helped your organisation are most welcome.

## INSTALLATION AND CONFIGURATION

### Hardware and software requirements

#### Hardware requirements

The thirdman correlation search is platform agnostic.

#### Software requirements

To function properly, thirdman requires the following software:

- Splunk Enterprise 6.2+
- Splunk Common Information Model 4.2+

It is essential that your authentication sourcetypes' fields be mapped to the CIM's authentication datamodel and the datamodel should be accelerated before using this app.

The following are optional:

- Splunk App for Enterprise Security 3.2.2+

The correlation search (Access - Third Man Access Behaviour Detected - Rule), is designed for the Splunk App for Enterprise Security (ES) 3.2.2+.

#### Splunk Enterprise system requirements

This app requires significant resources, but the exact requirements depend on a range of factors. The system requirements for the Splunk App for Enterprise Security can be used as a guide (http://docs.splunk.com/Documentation/ES/latest/Install).

#### Download

The thirdman correlation search app is currently available here: https://github.com/doksu/thirdman and will be released on splunkbase.splunk.com after the Apptitude competition closes.

#### Installation steps

To install this app on your supported platform, follow these steps:

1. Install this app on your search head/s (see more details below) as you would with any other app
2. See instructions below for details on configuration

##### Deploy to single server instance
Steps are the same as above, however step two will be redundant.

##### Deploy to distributed deployment with Search Head Pooling
Search Head Pooling is deprecated by the vendor.

##### Deploy to distributed deployment with Search Head Clustering
In step two above, follow your normal app distribution practices using a search head cluster deployer server.

##### Deploy to Splunk Cloud
Please contact Splunk Support.

## USER GUIDE

### Key concepts for thirdman

Detection of third man events is predicated on the number of unique vectors a user has for a given successful authentication event, within a given time period. Unique vectors are: what the user logged into (dest), when they logged in (we use an abstraction of time - see search string), where they logged in from (not their IP address, but the name of the organisation that is responsible for the autonomous system governing their IP address) and lastly how they logged in (the app that was used, e.g. ssh). 

### Data types

None.

### Lookups

The thirdman contains 1 lookup file.

**tm_asn**

The tm_asn lookup provides an IPv4 CIDR notation Autonomous System lookup.

- File location: ./lookups/tm_asn.csv
- Lookup fields: ip
- Lookup contents: ip, asn, autonomous_system

### Configure thirdman

The thirdman app does not require any configuration per se, however if you wish to use the provided real-time alert, calibration is required. Each organisation's expected patterns of user authentication behaviour are different. Organisations with strict policies around external access and working-hours (e.g. financial institutions), may choose to decrease the alert's threshold and search period. On the other hand, organisations with users that naturally exhibit greater variability in their expected authentication patterns and long seasonal fluctuations (e.g. universities), may find increasing the alert's threshold and extending the searches' duration yields better results. This app's dashboard can be used to determine the optimal search period and threshold for your organisation. The sensitivity of the alert can be modified by changing the unique_vectors predicate in the alert's where statement. A unique_vectors threshold of >2 would be considered normal, >3 low and >4 very low sensitivity.

To configure the third man alert:
1. Use the 'Find the Third Man' dashboard to determine the optimal calibration for your alert (minimising false-positives)
2.1. If you have ES, customise the correlation search ("Access - Third Man Access Behaviour Detected - Rule") and enable
2.2. If you don't have ES, clone the "Third Man Alert Template" saved search and customise, as per your requirements

To increase this app's efficacy, if your organisation has network "zones", it's recommended to replace the lookup's subnets your organisation uses with those zones. As a trivial example, if all your infrastructure were in 10.1.0.0/16, all client devices in 10.2.0.0/16 and all VPN clients in 10.3.0.0/16; remove the existing entry for 10.0.0.0/8 and replace it with those three entries. In this way, the autonomous system vector will have greater precision.

N.B. If the maximum size of static lookup files has not yet been increased from the default in your environment, you will need to do so by adding a stanza such as this to limits.conf:

[lookup]

max_memtable_bytes=200000000

### Troubleshoot thirdman

***Problem***
***Cause***
***Resolution***

### Upgrade thirdman

### Example Use Case ###

Simply install this app as you would any other, then use the 'Find the Third Man' dashboard to detect the historical use of misappropriated credentials. If you would like to be alerted of new third man events, see the alert configuration above.
