# SAP BTP Identity Security Recommendations Lab

This lab converts the Identity Authentication and Identity Provisioning rows from the
exported **SAP BTP Security Recommendations** page into executable configuration and
verification exercises.

> **Source and currency.** The source export was captured on 2026-10-22. The
> configuration links below are the related links in the recommendation table. SAP Help
> is dynamically rendered, so menu names and screenshots can change; use the linked
> page as the authority for the current UI and tenant-specific prerequisites.

## Scope

This lab covers:

- 18 recommendations for **SAP Cloud Identity Services - Identity Authentication (IAS)**.
- 8 recommendations for **SAP Cloud Identity Services - Identity Provisioning (IPS)**.

It does not automatically apply changes. Perform the exercises first in a non-production
tenant, use a test application and test users, and record the original setting before
changing it. A tenant administrator (or a delegated administrator with the required
permissions) is required for most exercises.

## Lab 0 - Prepare and capture a baseline

1. Obtain the IAS and IPS tenant URLs, the BTP subaccount, and a test application. Record
   whether the scenario is business-to-employee (B2E), business-to-business (B2B), or
   business-to-consumer (B2C). Do not enable public registration or social sign-on merely
   to complete this lab.
2. Create two test users: one ordinary user and one controlled test administrator. Do not
   use a personal or production administrator account for testing.
3. Create a test group for policy exercises and, if applicable, a test corporate identity
   provider (IdP). Confirm a recovery administrator and a second browser session are
   available before changing authentication or administrator settings.
4. Export or screenshot the current IAS application authentication/access settings,
   password policy, trusted domains, branding, mail, alert, and administrator settings.
   In IPS, record source/target systems, connector authentication type, job schedules,
   trace properties, and certificate expiration dates.
5. For each exercise, record: **before**, **change**, **test result**, **rollback**, and
   **owner**. Never paste passwords, private keys, tokens, or certificate contents into
   the lab record.

## Lab 1 - Protect administrators and end users in IAS

### 1.1 Protect the administration console with MFA

**Recommendation:** Critical - Default Authentication of Administrators.

1. Open the linked guide, [Configure Risk-Based Authentication for an
   Application](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/bc52fbf3d59447bbb6aa22f80d8b6056.html).
2. Identify the administration-console application in IAS and inspect its current
   authentication rules.
3. Add or update a rule that requires a second factor for administrators. Keep a
   recovery path for the designated recovery administrator.
4. Save the rule and confirm its order relative to other rules. Test with the controlled
   administrator in a private browser session; verify that password-only access is not
   accepted.
5. Record the rule, test timestamp, and rollback method. Do not disable the recovery
   path until the new factor has been tested.

### 1.2 Enforce a suitable password policy

**Recommendation:** Recommended - Password Policies.

1. Read [Configure Custom Password
   Policy](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/67bece21d0fb4e66b3341e9093c64f58.html).
2. Decide whether password authentication is actually required. Prefer federation or
   passwordless authentication where the business scenario supports it.
3. If passwords remain required, select Enterprise or create a custom policy stronger
   than the corporate minimum. Configure length, character requirements, history,
   expiration, and lockout in accordance with the linked guide and company policy.
4. Apply the policy only to the intended application or tenant scope. Do not weaken an
   existing policy to make a test user work.
5. Test a compliant password, a rejected weak/reused password, and the password-change
   flow. Record the effective policy and the affected applications.

### 1.3 Keep self-registration disabled unless the use case requires it

**Recommendation:** Critical - Self-Registration of End Users.

1. Read [Configure User Access to the
   Application](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/8b147c46269243dd8e5a42feb8b5a2ef.html)
   and the [B2B/B2C scenarios](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/fb9898deed834302ace22e0c2297c631.html).
2. Open the test application and set user access to existing/managed users (the
   `internal` behavior). Confirm self-registration is not exposed.
3. Test the registration URL in a private browser session and confirm it is unavailable
   or restricted.
4. If a genuine B2C use case requires registration, document the owner, allowed domains,
   verification method, CAPTCHA, and review date before enabling it. Re-test that an
   unapproved domain cannot register.

### 1.4 Keep social sign-on disabled unless required

**Recommendation:** Critical - Social Sign-On of End Users.

1. Read [Enable or Disable Social Sign-On for an
   Application](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/ff12d3da94914c95a29397270068d0e7.html).
2. In the test application, keep social sign-on disabled (`internal`) unless the
   documented B2C use case requires it.
3. Test the login page and verify that no unapproved social provider is offered.
4. If enabling it, allow only the selected provider, document the privacy and account
   linking decision, and test both first login and account recovery.

### 1.5 Prefer passwordless end-user authentication

**Recommendation:** Advanced - Default Authentication of End Users.

1. Review the linked guides for [Kerberos
   Authentication](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/b0301657df074ab081ab7556854aca56.html),
   [user certificates](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/1268ff5987d8484698f77cd24c91efa5.html),
   [FIDO2](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/ebf830604b334e09a0b0a7d553f4bd9f.html),
   [tenant-default RBA](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/1aab51ae62b94f79b4c6dac7a00857c2.html?locale=en-US&state=PRODUCTION&version=Cloud),
   and [application RBA](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/549336745eda4dfdbdaecd9a8b00c8b1.html?locale=en-US&state=PRODUCTION&version=Cloud).
2. Choose one method supported by the test users and application. Configure it in the
   test application first.
3. Add an RBA rule that excludes **User Name and Password** for the intended users or
   groups, while retaining an explicitly tested recovery path.
4. Test successful passwordless login, an unregistered-device response, and recovery.
   Roll back the rule if the recovery path is not usable.

## Lab 2 - Manage IAS lifecycle, trust, and authorization

### 2.1 Retain IAS audit data

**Recommendation:** Advanced - Audit Data.

1. Read [Access Audit Logs (AWS, Azure
   Infrastructure)](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/a3e793c9096c4fee9c98ecdcb8791cfc.html).
2. Confirm the tenant's audit log destination and the Cloud Foundry SAP Audit Log
   retention (the recommendation notes 90 days).
3. Schedule an authorized export before the retention boundary and store it in the
   approved, access-controlled archive.
4. Retrieve a test period, verify that administrator and authentication events are
   present, and record the job owner, retention period, and restore/search procedure.

### 2.2 Govern third-party IdP trust

**Recommendation:** Recommended - Integration of 3rd-Party Identity Providers.

1. Read [Corporate Identity
   Providers](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/19f3eca47db643b6aad448b5dc1075ad.html).
2. Inventory each configured corporate IdP, trust type, signing certificate, claims,
   groups, and owner. Remove unused trusts only after confirming dependencies.
3. Validate issuer, audience, signing certificate, redirect/ACS values, and group
   mappings against the IdP. Use a test application or test group.
4. Test successful federation, an invalid/expired assertion, and an unauthorized group.
   Record certificate rotation and emergency rollback steps.

### 2.3 Synchronize IAS with the leading identity system

**Recommendation:** Advanced - Identity Lifecycle.

1. Review the [Identity Provisioning](https://help.sap.com/docs/IDENTITY_PROVISIONING)
   service documentation.
2. Identify the authoritative source, attributes, groups, joiner/mover/leaver process,
   and target applications. Decide which system owns each attribute.
3. Configure an IPS source-to-Identity Directory job in the test tenant. Map only the
   required attributes and groups; do not copy secrets or unnecessary personal data.
4. Run a preview or small test batch. Verify create, update, disable, and delete behavior
   with the two test users, then document reconciliation and retry handling.

### 2.4 Minimize critical administrator authorizations

**Recommendation:** Recommended - Critical Administrator Authorizations.

1. Read [List Administrators](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/c79a5c6d53d54d06a9341b9093e7b33c.html).
2. Export the administrator list and classify each account by owner, purpose, last use,
   and required permission.
3. Remove inactive accounts, convert shared accounts to named accounts, and separate
   **Manage Corporate Identity Providers**, **Manage Tenant Configuration**, and
   **Manage Users** wherever possible.
4. Test that an ordinary administrator can perform only the delegated task and cannot
   change a higher-risk setting. Review this list at a defined interval.

### 2.5 Publish legal and privacy information where required

**Recommendation:** Advanced - Disclaimer.

1. Read [Configure Links Section on Logon
   Screen](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/060c03245a0a4ad69fc4a87de3329671.html).
2. Obtain approved legal disclaimer, privacy statement, copyright text, and URLs from
   the legal/privacy owner.
3. Add the links to the relevant application or tenant login experience.
4. Test visibility, HTTPS links, language variants, and accessibility. Record approval
   and an owner for future changes.

### 2.6 Use a corporate domain

**Recommendation:** Advanced - Corporate Domain.

1. Read [Use Custom Domain in Identity
   Authentication](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/c4db840ff2464e12ab68d94efb0769c3.html).
2. Select a dedicated corporate hostname and verify DNS ownership and certificate
   requirements for the Customer Domain service.
3. Configure the domain in the test tenant, complete the ownership verification, and
   bind it to the intended IAS endpoint/application.
4. Test login, logout, redirects, certificate validity, and the old URL behavior before
   communicating the new address to users.

### 2.7 Send mail through the corporate mail server

**Recommendation:** Advanced - Corporate Mail Server.

1. Read [Configure Mail Server for Application
   Processes](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/ccc7ba11dfd743128b7926b7dc2ca8c3.html).
2. Obtain the approved SMTP host, port, TLS mode, sender address, authentication method,
   and SPF/DKIM/DMARC requirements.
3. Configure the test tenant and verify that the sender is an approved corporate address.
4. Trigger a test activation or password notification. Check delivery, TLS, headers,
   bounce handling, and that no credentials appear in logs.

### 2.8 Apply corporate branding

**Recommendation:** Advanced - Corporate Brand.

1. Read [Configure a Branding Style for an
   Application](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/32f8d337f0894d269f5f89956803efac.html).
2. Obtain approved logo, colors, text, and accessibility alternatives.
3. Configure the branding style for the test application without changing the default
   tenant-wide experience.
4. Test desktop/mobile rendering, localization, links, and that the domain and branding
   make phishing-resistant user recognition easier.

### 2.9 Alert administrators and end users

**Recommendations:** Recommended - Administrator Alerts and End-User Alerts.

1. Configure administrator notifications using [Send System Notifications via
   E-Mails](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/aa04a8b6c60a440f866bfe30000e1e89.html).
   Include certificate expiry, system notifications, and new-administrator events.
2. Configure end-user notifications using [Send Security Alert
   E-Mails](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/c97746478abe4a96b7a294ccc4633daa.html).
3. Enable at least email-address, login-name, credential, and TFA-device
   change/activation alerts.
4. Trigger a controlled test event, verify receipt and recipient scope, and record how
   alerts are monitored and escalated.

### 2.10 Restrict self-registered values and automated abuse

**Recommendations:** Advanced - Self-Registration of User Names and Self-Registration
and CAPTCHA.

1. Use [Restrict User Attributes Values via Exclude
   Lists](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/cb108c22162741e2a50b7cf8466148bf.html)
   to block reserved names and unwanted email domains, but only when self-registration
   is intentionally enabled.
2. Read [Protecting Application Forms with Google
   reCAPTCHA](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/b84ce17a2e264eaba644723339cb2f93.html)
   and [Enable MTCaptcha for Application
   Forms](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/6e2f44ad02554b40b575f8e37dacb280.html).
3. Configure one approved CAPTCHA provider, protect the registration/login forms, and
   test valid, invalid, and rate-limited submissions.
4. Confirm the block list cannot prevent legitimate corporate users and that CAPTCHA
   credentials are stored only in the tenant configuration.

### 2.11 Define tenant-wide risk-based authentication

**Recommendation:** Advanced - Authentication Policies.

1. Read [Configure Default Risk-Based Authentication for All Applications in the
   Tenant](https://help.sap.com/docs/IDENTITY_AUTHENTICATION/6d6d63354d1242d185ab4830fc04feb1/1aab51ae62b94f79b4c6dac7a00857c2.html).
2. Define policy objectives by group, user type, IP range, authentication method, and
   corporate attribute. Start with reportable/test scope where available.
3. Add rules from most specific to least specific and define the default action. Avoid a
   broad deny rule until the recovery administrator has been tested.
4. Test each rule with matching and non-matching users and networks. Record rule order,
   effective action, and rollback.

## Lab 3 - Harden IPS authentication, tracing, and audit

### 3.1 Retain IPS audit logs (Neo tenants)

**Recommendation:** Advanced - Audit Data.

1. Read [Audit Logs: Retention and Retrieval APIs
   (Neo)](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/e6b90a5575f8401bb8469904a159788e.html).
2. Confirm whether the tenant is Neo and whether it is a bundle tenant. The table notes
   201-day retention and that bundle-tenant OAuth clients cannot increase it.
3. Create an authorized retrieval job using the documented API/client scopes. Store
   output in an access-controlled archive and verify timestamps and event coverage.
4. Schedule retrieval before the retention limit and document the archive retention,
   encryption, access review, and restore test.

### 3.2 Keep IPS entity tracing off by default

**Recommendation:** Recommended - Service Specific.

1. Read the [Cloud Identity Services data-storage security and property
   lists](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/security-information?version=Cloud)
   and [list of properties](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/list-of-properties?version=Cloud).
   For Neo tenants, also see the [Neo data-storage
   security](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/75204c6a11544b88ac5c10a0a96bd7f8.html#data-storage-security)
   and [Neo property list](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/d6f3577f30ec4af98e734b0126a60e37.html).
2. Verify `ips.trace.failed.entity.content`, `ips.trace.created.entity.content`,
   `ips.trace.updated.entity.content`, and `ips.trace.skipped.entity.content` are
   `false` in normal operation.
3. For a controlled troubleshooting window, enable only the required trace, set an
   owner and expiry time, run the smallest possible test job, and remove sensitive
   output from the permitted log/archive according to policy.
4. Turn the trace off, verify the property values, and record the incident/change
   reference. Do not leave entity content tracing enabled for convenience.

### 3.3 Use certificate-based outbound connector authentication

**Recommendation:** Critical - Strong Authentication (provisioning systems).

1. Read [Manage Certificates (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/manage-certificates?version=Cloud)
   or [Manage Certificates
   (Neo)](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/86d06a0fadec41f0a53dd74b2e77fa96.html).
2. Inventory every IPS source/target connector and mark which still uses basic
   authentication. Confirm the endpoint supports certificate authentication.
3. Create or import a certificate using the documented key handling process. Never
   export or commit a private key to the repository.
4. Configure the connector with the certificate, validate the chain and subject,
   execute a small test job, and then remove the basic credential if successful.
5. Record expiry, owner, renewal lead time, and rollback. Test renewal before the
   certificate approaches expiry.

### 3.4 Use certificate-based inbound authentication

**Recommendation:** Critical - Strong Authentication (real-time/proxy scenarios).

1. Read [Manage Inbound Certificates (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/manage-certificates-for-inbound-connection?version=Cloud)
   or [Manage Inbound Certificates
   (Neo)](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/952e7c78c3bd465a864a52b3a0786fa5.html).
2. Identify every inbound caller and confirm which certificate subject/issuer it should
   present. Remove stale or duplicate certificates after dependency review.
3. Upload/register the caller certificate or CA as described by the linked guide.
   Configure the caller for mutual TLS and validate hostname and certificate chain.
4. Test an authorized request, an expired certificate, an untrusted issuer, and a
   certificate with the wrong subject. Record renewal and emergency rollback steps.

### 3.5 Require secure communication protocols

**Recommendation:** Critical - Encryption, Data in Transit.

1. Read [Communication Protocol (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/security-information?version=Cloud#communication-protocol)
   or [Communication Security
   (Neo)](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/ddd552d8a5e742c1bf89599ca3b36c6e.html).
2. Inventory every URL in BTP cockpit destinations and IPS source/target properties.
   Replace HTTP, plaintext, or obsolete endpoints with the documented secure protocol
   and hostname.
3. Test certificate validation, hostname verification, redirects, and a small
   provisioning job. Confirm the job fails closed when TLS validation is invalid.

### 3.6 Store secrets as credential properties

**Recommendation:** Critical - Encryption, Data in Transit.

1. Read [Credential Properties (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/property-types?version=Cloud#credential-properties)
   or [Credential Properties
   (Neo)](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/1780d5d3f5a14fc1a90946e3aa10fb60.html#credential-properties).
2. Find passwords, client secrets, API keys, and tokens stored as ordinary properties or
   in destination URLs. Replace each with the supported credential-property type.
3. Run a test job, verify the connector resolves the secret, and confirm the value is
   masked in the UI and absent from exported logs. Rotate any secret exposed by the old
   configuration.

### 3.7 Do not use `TrustAll` in production

**Recommendation:** Critical - Encryption, Data in Transit.

1. Read [List of Properties (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/list-of-properties?version=Cloud),
   [Communication Protocol (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/security-information?version=Cloud#communication-protocol),
   and the [Neo communication
   guide](https://help.sap.com/docs/IDENTITY_PROVISIONING/f48e822d6d484fa5ade7dda78b64d9f5/ddd552d8a5e742c1bf89599ca3b36c6e.html).
2. Search every connector and destination for `TrustAll` and record the reason and
   environment. Remove it from all productive configurations.
3. Configure the correct server CA/chain and hostname, then test a valid certificate
   and an invalid/untrusted certificate. The invalid case must fail.
4. If a temporary test requires `TrustAll`, add an expiry/change reference and remove it
   immediately after the test; verify the property is absent.

### 3.8 Maintain more than one IPS administrator

**Recommendation:** Recommended - Critical Permissions.

1. Read [Manage Administrators (Cloud Identity
   Services)](https://help.sap.com/docs/cloud-identity-services/cloud-identity-services/manage-administrators?version=Cloud)
   or [Manage Authorizations
   (Neo)](https://help.sap.com/docs/identity-provisioning/identity-provisioning/manage-authorizations-in-neo-environment?version=Cloud).
2. List the current administrators, identify the owner of each account, and remove
   inactive or shared accounts.
3. Add a second named administrator with only the permissions required for the role.
   Have both administrators test login and a permitted administrative task.
4. Record the break-glass process, access review cadence, and how an administrator is
   removed without locking out the tenant.

## Completion checklist

- [ ] Every recommendation has an owner, target scope, before/after value, and review date.
- [ ] MFA/RBA changes were tested with a recovery administrator.
- [ ] Self-registration and social sign-on are disabled unless a documented B2C/B2B use
  case requires them.
- [ ] Administrator permissions are named, minimal, and periodically reviewed.
- [ ] IdP trust, custom domains, SMTP, branding, and alerting were tested end to end.
- [ ] IAS and IPS audit data is exported before retention limits and is access controlled.
- [ ] IPS entity-content tracing is disabled outside an approved troubleshooting window.
- [ ] Basic connector credentials were replaced where certificate authentication is
  supported, and certificate renewal dates are monitored.
