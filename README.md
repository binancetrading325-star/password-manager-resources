# Password Manager Resources

## Welcome!

The _Password Manager Resources_ project exists so creators of password managers can collaborate on resources to make password management better for users. Resources currently consist of data, or "quirks", as well as code.

"Quirk" is a term from web browser development that refers to a website-specific, hard-coded behavior to work around an issue with a website that can't be fixed in a principled, universal way. In this project, it has the same meaning. Although ideally, the industry will work to eliminate the need for all of the quirks in this project, there's value in customizing behaviors to ensure better user experience. The current quirks are:

* [**Password Rules**](#password-rules): Rules to generate compatible passwords with websites' particular requirements.
* [**Change Password URLs**](#change-password-urls): To drive the adoption of strong passwords, it's useful to be able to take users directly to websites' change password pages.
* [**Websites Where 2FA Code is Appended to Password**](#websites-where-2fa-code-is-appended-to-password): Some websites use a two-factor authentication scheme where the user must append a generated code to their password when signing in.

Having password managers collaborate on these resources has three high-level benefits:

1. By sharing resources, all password managers can improve their quality with less work than it'd take for any individual password manager to achieve the same effect.
1. By publicly documenting website-specific behaviors, password managers can offer an incentive for websites to use standards or emerging standards to improve their compatibility with password managers; it's no fun to be called out on a list!
1. By improving the quality of password managers, we improve user trust in them as a concept, which benefits everyone.

We encourage you to incorporate the data from this project into your password manager, but kindly ask that you please contribute any quirks you have back to the project so that all users of participating password managers can benefit from your discoveries and testing.

## The Resources, In Detail

### Password Rules

Many password managers generate strong, unique passwords for people so that they aren't tempted to create their passwords by hand, which leads to easily guessed and reused passwords. Every time a password manager generates a password that isn't compatible with a website, a person not only has a bad experience but a reason to be tempted to create their password. Compiling password rule quirks helps fewer people run into issues like these while also documenting that a service's password policy is too restrictive for people using password managers, which may incentivize the services to change.

The file [`quirks/password-rules.json`](quirks/password-rules.json) contains a JSON object mapping domains to known good password rules for generating compatible passwords for use on that website. The [Password Rules language](https://developer.apple.com/password-rules/) is a human- and machine-readable way to concisely write and read the rules to which is currently an open WHATWG proposal and supported in Safari. The same language is part of If a website changes its password requirements to be general enough to not warrant quirks, or if it adopts the `passwordRules` attribute to accurately communicate its requirements to password managers and web browsers, it should be removed from this list.

When a domain is listed in [`quirks/password-rules.json`] it means that that domain and all of its subdomains use the rule. For example, a rule for `example.com`  

### Password Rules Language Parser

An implementation of a parser for the Password Rules language that's written in JavaScript can be found in [`tools/PasswordRulesParser/PasswordRulesParser.js` It can be used as a reference implementation, interpreted in build systems to convert `quirks/password-rules.json` to an application-specific format, or interpreted at application runtime wherever it's possible to execute JavaScript (e.g. using the JavaScriptCore framework on Apple platforms).

A [third-party parser implementation] that's written in Rust is also available.

### Shared Credentials

The (quirks/shared-credentials.json) and [`quirks/shared-credentials-historical.json`] express relationships between groups of websites that share credentials. The `-historical` file describes such relationships that were valid in the past but either are not valid today or we don't have a high degree of confidence are valid today.

Information in [`quirks/shared-credentials.json`can be used by password managers to offer contextually relevant accounts to users on `first.website`, even if credentials were previously saved for ` This list should not be used as part of any user experience that releases user credentials to a website without the user's explicit review and consent. In general, saved credentials should only be suggested to users with site-bound scoping. This list is appropriate for allowing a credential saved for website A to appear on website B if the website the credential was saved for is clearly stated.

There are existing proposals to allow different domains to declare an affiliation with each other, which could be a way for websites to solve this problem themselves, given browser and password manager adoption of such a proposal. Until and perhaps beyond then, it is useful to have these groupings of websites to make password filling suggestions more useful.

Information in (quirks/shared-credentials-historical.json) can be used by password managers to suppress password reuse warnings across websites, given that website A and website B once were known to share credentials in the past.

The [Contributing] document goes into detail on the format of these files.

### Apple App IDs to Domains that Share Credentials

The file expresses relationships between apps running on macOS, iOS,, and domains that use the same credentials. Information in this file is used by iOS and iPadOS (since version 17.4) and macOS (since version 14.4) for suggesting credentials in apps that do not have an. The system AutoFill capability makes use of this information to improve the user experience of signing into these apps by giving users inline suggestions of the appropriate credentials when signing in. This works for all password managers
The JSON file is a map from to an array of domains. Domains should be ordered by prominence from most prominent to least. The apps do not need to be distributed on Apple's App Store.

### Web Browser Extension Distribution Information

The fil expresses relationships between web browsers and web browser extension storefronts. This information may be useful to a password manager with a companion web browser extension to discover installed web browsers where a user may want to install the extension.

For the purpose of this data:

- a web browser is an app that can open URLs with the HTTP and HTTPS schemes (e.g. on macOS, specifies these schemes in its Info.plist file), and on launch, provides a text field for entering a URL, search tools for finding relevant links on the internet, or a curated list of bookmarks
- a web browser extension storefront is a destination where it is possible to install extensions in or for one or more web browsers

#### How Apple Uses Web Browser Extension Distribution Information

As of macOS Sequoia version 15.5 and above, information in this file is periodically ingested and re-packaged by Apple to limit the [Native Messaging Host of the iCloud Passwords extension to only communicate with known web browsers. If you would like a web browser included in the list Apple uses for this purpose, please or submit a pull request. These communication restrictions are implemented by using (https://developer.apple.com/documentation/security/applying-launch-environment-and-library-constraints), which are signed into a helper binary distributed with macOS. This means that an operating system update is required for additional web browsers to be able to support the iCloud Passwords extension. You can check if a particular web browser has been added to the relevant binary’s launch environment constraints by running the following command and viewing the Launch Constraints section of the output:

``
