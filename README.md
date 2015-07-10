# thirdman
Third Man Correlation Search for Splunk® Enterprise Security

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

The thirdman detects misappropriated credentials using an abstract statistical fingerprint of users' successful auth behaviour.

| Author | Doug Brown |
| App Version | 0.0.1 |
| Vendor Products | Splunk Enterprise Security 3.2.2 |
| Has index-time operations | true |
| Create an index | false |
| Implements summarization | Creates events in 'notable' summary index |

- Inspiration

The use of stolen credentials, via phishing or other means, is becoming one of the greatest challenges security professionals face. Based on the notion that people's behaviour form predictable patterns, I thought it must be possible to "profile" users' successful auth and therefore detect the use of misappropriated credentials. Like all good ideas, the mechanism of this correlation search came to me as I was trying to get to sleep one night and was consequently developed on the back of an envelope.

Regarding the name of this entry: At the time of submission to this competition, I needed to quickly come up with a name to succinctly describe this correlation search, but "Stolen Credentials" or similar, struck me as too mundane. The Third Man (an obvious movie reference) sounds better, and initially was just an allusion to a bad actor. However, after more thought, if we consider the first man to be the administrator, the second man to be the user, and the third man to be the bad actor, I think this analogy works quite well.

- How it works

This correlation search takes the CIM Authentication data model and enriches it with autonomous system information and an abstraction of time, then creates a statistical "fingerprint" of each users' behaviour in relation to what, when, where and how they successfully auth. A significant deviation from a user's pattern triggers the alert. Although this sounds relatively straightforward, importantly this correlation searches' ability to detect anomalous behaviour is derived from it's unique high-level abstraction of circumstances.

Most alerts of a similar nature use geographical topology lookups (known for being less than reliable), however this alert uses a logical network topology lookup to determine where a user authenticates from in relation to the structure of the Internet. This is significant because a typical user accesses the Internet from half a dozen or less Autonomous Systems (akin to postcodes at a global scale), in their day-to-day activities. By using statistics to model the likelihood of a user authenticating from a particular part of the Internet in relation to the other aspects of the tuple (what the resource is that's being accessed, when it's being accessed, and how it's being accessed), we can make a determination as to how confident we can be that the user credentials being used and the person they represent are one and the same.

##### Scripts and binaries

None.

#### Release notes

##### About this release

Version <0.0.1> of the thirdman is compatible with:

| Splunk Enterprise versions | 6.2+ |
| CIM | 4.2 |
| Platforms | Platform independent |
| Vendor Products | Splunk Enterprise Security 3.2.2 |
| Lookup file changes | Initial release of tm_asn lookup |

##### New features

The thirdman includes the following new features:

- Initial release

##### Fixed issues

Version <0.0.1> of the thirdman fixes the following issues:

- None

##### Known issues

Version <0.0.1> of the thirdman has the following known issues:

- May generate false-positives the first time a new user authenticates or if a user account has been dormant for the period the search is configured to run for; this will be addressed in the next release.

##### Third-party software attributions

Version <0.0.1> of the thirdman incorporates the following third-party software or libraries.

- None

#### Performance benchmarks

This app was developed using "The Audit Experiment" honeypot (https://github.com/doksu/splunk_auditd/wiki/The-Audit-Experiment).

##### Support and resources

**Questions and answers**

Please post questions to: answers.splunk.com

**Support**

This app is provided as is with no warranty, implied or otherwise. Feedback about possible improvements and good news stories of how this app has helped your organisation are most welcome.

## INSTALLATION AND CONFIGURATION

### Hardware and software requirements

#### Hardware requirements

The thirdman correlation search is platform agnostic.

#### Software requirements

To function properly, thirdman requires the following software:

- Splunk Enterprise 6.2+
- Splunk App for Enterprise Security 3.2.2+

Although designed for Splunk Enterprise Security (ES) 3.2.2+ running on Splunk 6.2+, this app is likely to work with earlier versions of ES and can be used in the absence of ES, after some modification. The Common Information Model (CIM) app is the only strictly mandatory requirement. Naturally, your authentication sourcetypes need to mapped to the authentication datamodel and that datamodel should be accelerated for at least the period this corelation search is configured for.

#### Splunk Enterprise system requirements

The same system requirements of the Splunk App for Enterprise Security apply to the thirdman correlation search (http://docs.splunk.com/Documentation/ES/latest/Install).

#### Download

The thirdman correlation search app is currently available here: https://github.com/doksu/thirdman and will be released on splunkbase.splunk.com after the Apptitude competition closes.

#### Installation steps

To install and configure this app on your supported platform, follow these steps:

1. Install this app on all search peers of ES (ie. indexing servers) as you would with any other app
1. Install this app on the ES search head/s (see more details below) as you would with any other app
1. Customise the correlation search as required in ES
1. Enable correlation search

##### Deploy to single server instance
Steps are the same as above, however step two will be redundant.

##### Deploy to distributed deployment with Search Head Pooling
Search Head Pooling should not be used with ES and is deprecated by the vendor.

##### Deploy to distributed deployment with Search Head Clustering
In step two above, follow your normal app distribution practices using a search head cluster deployer server.

##### Deploy to Splunk Cloud
Please contact Splunk Support.

## USER GUIDE

### Key concepts for thirdman

This alert is predicated on the number of unique vectors a user has for a given successful authentication event, within the time period the correlation search is configure for. Unique vectors are: what the user logged into (dest), when they logged in (we use an abstraction of time - see search string), where they logged in from (not their IP address, but the name of the organisation that is responsible for the autonomous system governing their IP address) and lastly how they logged in (the app that was used, e.g. ssh). 


### Data types

None.

### Lookups

The thirdman contains 1 lookup file.

** tm_asn **

The tm_asn lookup provides an IPv4 CIDR notation Autonomous System lookup.

- File location: ./lookups/tm_asn.csv
- Lookup fields: ip
- Lookup contents: ip, asn, autonomous_system

### Configure thirdman

Each organisation's expected patterns of user authentication behaviour are different. Organisations with strict policies around external access and working-hours (e.g. financial institutions), may choose to increase the alert's threshold and decrease the search period. On the other hand, organisations with users that naturally exhibit greater variability in their expected authentication patterns and long seasonal fluctuations (e.g. universities), may find reducing the alert's threshold and extending the searches' duration yields better results. The sensitivity of the alert can be modified by changing the unique_vectors predicate in the correlation searches' where statement. A unique_vectors threshold of >2 would be considered sensitive, >3 medium and >4 least sensitive.

### Troubleshoot thirdman

***Problem***
***Cause***
***Resolution***

### Upgrade thirdman

### Example Use Case ###

This app is designed to integrate with the Splunk App for Enterprise Security to detect the use of stolen credentials.
