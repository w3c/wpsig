# Using SPC to fulfill PSD2 Requirements for SCA and Dynamic Linking

Status: This is a draft document without consensus. The Editor hopes that the Web Payment Security Interest Group will develop this document and publish an Interest Group Note. 

Note: This document has not been updated in light of the evolution of passkeys.

Questions? Ian Jacobs &lt;ij@w3.org>.

[Secure Payment Confirmation
(SPC)](https://github.com/w3c/secure-payment-confirmation) is a new (draft)
Web API to support streamlined authentication during a payment
transaction. In this document we explain how SPC can be used to
fulfill the Strong Customer Authentication (SCA) and Dynamic Linking
requirements of Payment Services Directrive (PSD2).

Note: In this document we assume that SPC leverages FIDO Authentication.  We therefore recommend familiarity with <a href="https://fidoalliance.org/">FIDO Authentication</a> and specifically the <a href="https://www.w3.org/Webauthn/">Web Authentication specification</a>.

## Background

SPC is being designed for use within a wide range of payment methods
(cards, open banking, proprietary payment methods, etc.) and within a
variety of authentication protocols (including, but not limited to,
EMV® 3-D Secure). W3C is working closely with the FIDO Alliance,
EMVCo, and other partners to achieve a number of benefits through SPC:

* **Authentication Streamlined for Payment**. We expect SPC to build on the FIDO authentication experience in several ways, by further accelerating authentication (compared to one-time passcodes), requiring fewer user gestures, offering a predictable user experience across sites, and avoiding redirects.
* **Scalable and Ubiquitous**. <a name="scale"></a> SPC supports streamlined authentication across multiple merchant sites following a single enrollment.
* **Simpler and more Secure Front-end Deployment**. The browser manages the display of the payment confirmation experience, removing the need for other parties (e.g., issuing banks or payment apps) to do so. In addition, enabling payment service providers or others to authenticate the user can reduce the need to embed code provided by a Relying Party in a Web page, reducing security risks. Reducing the need for redirects should also simplify solutions.
* **Designed to Meet Regulatory Requirements**. The standardized payment confirmation user experience is designed to help entities fulfill regulatory requirements (e.g., strong customer authentication and dynamic linking under PSD2) and other customer authentication use cases.

In this document we focus on the last point: how to use SPC to fulfill
PDS2 requirements.

## Unique SPC Features

The above benefits are grounded in a small number of unique features that distinguish SPC from FIDO authentication "out of the box":

* **Browser-native UX for payment confirmation**. <a name="spc-ux"></a> The browser (or secure hardware) provides a consistent and efficient authentication UX across merchant sites and relying parties. A browser-native user experience should modestly improve transaction security compared to ordinary Web content rendered in an iframe.
* **Cryptographic evidence**. <a name="spc-crypto-evidence"></a> Payment confirmation generates cryptographic evidence of the user's confirmation of payment details.
* **Third-Party Enrollment**. <a name="spc-3p-enrollment"></a> With FIDO, it is possible to authenticate from a cross-origin iframe, but one must register an authenticator in a first party context. With SPC, it is possible to register an authenticator from a cross-origin iframe, which is a common payment ecosystem use cases.
* **Cross-origin authentication**. <a name="spc-cross-origin"></a> With FIDO, the Relying Party that creates FIDO credentials is the only origin that can generate an assertion with those credentials to authenticate the user. With SPC, any origin can generate an assertion during a transaction even by leveraging another Relying Party's credentials.

These features are used to satisfied two key PSD2 requirements:

* **Strong Customer Authentication (SCA)**: <a name="psd2-sca"></a>FIDO Authentication is used to fulfill the SCA requirement. SPC's cross-origin authentication reduces user experience friction by requiring fewer enrollments.
* **Dynamic linking**: <a name="psd2-dl"></a> SPC adds to browsers built-in support for the display of dynamic linking data and cryptographic proof (via FIDO) of what data was displayed.

## General SPC Flow

SPC consists of two phases: enrollment and authentication. The
following flow diagram provides a general view of the parties involved
in SPC Authentication. 

The flow diagram describes general roles. Each specific payment
ecosystem or authentication protocol will determine which parties play
which roles, including:

* Account Provider (e.g., ASPSP, or issuing bank, or ACS with EMV® 3-D Secure, or SRC System with EMV® Secure Remote Commerce)
* Payment Service Provider (e.g., PISP)
* Merchant (see FAQ question below on delegation use case)

The centrality of the Relying Party in a payment system has an impact on SPC's scalability.

<img src="spc-general.png" alt="General SPC Flow Diagram; PUML source available"/>

### SPC Integration into Backend Protocols

As the flow diagram indicates, during authentication the merchant
interacts with the Relying Party at two moments:

* To request any SPC Credentials associated with an instrument, and
* To provide an SPC Assertion for verification.

This communication may take place using a variety of protocols. At the present time, for example, we are discussing SPC integration with EMV® 3-D Secure, EMV® Secure Remote Commerce, and [Open Banking](https://docs.google.com/document/d/1qjBPa6l0EM9A3sLl9neccq_8UPHe90jXTGXqcbge2vQ/edit).

EMVCo has indicated that they anticipate that EMV® 3-D Secure version
2.3 will include support for SPC. For related information, see [How
Does EMV® 3-D Secure Help to Meet European Regulation While Supporting
the Global Fight Against CNP
Fraud?](https://www.emvco.com/emv_insights_post/how-does-emv-3-d-secure-help-to-meet-european-regulation-while-supporting-the-global-fight-against-cnp-fraud/)

## Security Analysis

SPC leverages FIDO Authentication and builds on FIDO security properties described in the [FIDO Security Reference](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-security-ref-v2.0-id-20180227.html). The SPC analysis builds on and refers to that work.

Note:

* Bracketed labels such as "[SG-1]" are defined in the [FIDO Security Reference](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-security-ref-v2.0-id-20180227.html). However, labels that begin with "SPC" are defined in this document.
* See also: [FIDO Certification Program](https://fidoalliance.org/certification/) for information about certification of FIDO servers, clients, and authenticators.

### Security Goals

SPC is especially concerned with the following security goals identified in the FIDO Security Reference:

* [SG-1] Strong User Authentication: Authenticate (i.e., recognize) a user and/or a device to a relying party with high (cryptographic) strength. This is the goal most closely related to the [PSD2 SCA requirement](#psd2-sca). 
* [SG-14] Transaction Non-Repudiation: Provide strong cryptographic non-repudiation for secure transactions. This is the goal most closely related to the [PSD2 dynamic linking requirement](#psd2-dl). The SPC assertion is created upon positive user verification and thus represents the customer’s acknowledgement and consent of the transaction details.

Note:

* SPC focuses on user authentication. SPC may also help Relying Parties protect against other forms of fraud (e.g., fradulent merchants) but payments systems manage those risks through other mechanisms as well.

### Security Measures

SPC builds on the Security Measures described in the FIDO Security Reference. This table maps SPC features to the most relevant FIDO security measures:

| FIDO Security Measure | SPC Feature(s) |
| --------------------- | ----------- |
| [SM-5] User Consent   | [browser-native UX for payment confirmation](#spc-ux) |
| [SM-10] Transaction Confirmation | [browser-native UX for payment confirmation](#spc-ux); [cryptographic evidence](#spc-crypto-evidence) |
| [SM-14] AppID Separation | [cross-origin authentication](#spc-cross-origin). SPC explicitly relaxes this constraint compared to FIDO. |

### Security Assumptions <a name="security-assumptions"></a>

An SPC security analysis begins with a security analysis of the underlying
FIDO system. The following assumptions identified in the FIDO Security Reference
are particularly relevant as background to evaluating SPC:

* [SA-1] The Authenticator and its cryptographic algorithms and parameters (key size, mode, output length, etc.) in use are not subject to unknown weaknesses that make them unfit for their purpose in encrypting, digitally signing, and authenticating messages. (Thus, the SPC Assertion resists tampering between when it leaves the browser and when the Relying Party verifies it and ensures it meets Relying Party expectations.)
* [SA-3] Applications on the user device are able to establish secure channels that provide trustworthy server authentication, and confidentiality and integrity for messages (e.g., through TLS). 
* [SA-4] The computing environment on the FIDO user device and the and applications involved in a FIDO operation act as trustworthy agents of the user. 
* [SA-6] The computing resources at the Relying Party involved in processing a FIDO operation act as trustworthy agents of the Relying Party.

In particular, we make the following assumptions specific to SPC:

* [SPC-SA-1] <a name="spc-sa-1"></a> Display integrity: Once SPC has been called, code running in the browser (e.g., in the JavaScript environment) cannot tamper with the display of transaction information by the browser.
* [SPC-SA-2] <a name="spc-sa-2"></a> Signing integrity: The transaction information displayed by the browser is the same information that is signed by the authenticator.

## Detailed Evaluation of PSD2 Requirements and SPC

The PSD2 requirements below are drawn from [Regulatory Technical Standards on strong customer authentication and secure communication under PSD2 ](https://www.eba.europa.eu/sites/default/documents/files/documents/10180/1761863/314bd4d5-ccad-47f8-bb11-84933e863944/Final%20draft%20RTS%20on%20SCA%20and%20CSC%20under%20PSD2%20%28EBA-RTS-2017-02%29.pdf) (23 February 2017 edition).

Note: This evaluation approach is based on [How FIDO Standards Meet PSD2’s Regulatory Technical Standards Requirements On Strong Customer Authentication](https://fidoalliance.org/how_fido_meets_the_rts_requirements/) from the FIDO Alliance. Below, this document is identified as "FIDO-PSD2".

### [RTS Chapter 1] General provisions

#### [RTS Article 3] – Review of the Security Requirements

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 3.1 | The implementation of the security measures referred to in Article 1 shall be documented, periodically tested, evaluated and audited in accordance with the applicable legal framework of the payment service provider by auditors with expertise in IT security and payments and operationally independent within or from the payment service provider | See FIDO-PSD2 for information about FIDO security certification. In addition, W3C provides publicly available tests for Web APIs to improve cross-browser interoperability.
| Article 3.3 | The audit review shall evaluate and report on the compliance of the payment service provider’s security measures with the requirements set out in this Regulation. |  See FIDO-PSD2 for information about FIDO security certification. W3C provides publicly available tests for Web APIs to improve cross-browser interoperability. |

### [RTS Chapter II] Security measures for the application of Strong Customer
Authentication

#### [RTS Article 4] – Authentication code

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 4.1 | The authentication shall be based on two or more elements which are categorised as knowledge, possession and inherence and shall result in the generation of an authentication code | This is achieved via FIDO; see FIDO-PSD2. The SPC assertion constitutes the authentication code, and includes information displayed to the user; see dynamic linking below. 
| Article 4.2 | For the purpose of paragraph 1, payment service providers shall adopt security measures ensuring that each of the following requirements is met: (a) no information on any of the elements referred to in paragraph 1 can be derived from the disclosure of the authentication code; (b) it is not possible to generate a new authentication code based on the knowledge of any other authentication code previously generated; (c) the authentication code cannot be forged. |  These are achieved via FIDO; see FIDO-PSD2.
| Article 4.3 | Payment service providers shall ensure that the authentication by means of generating an authentication code includes each of the following measures: (a) where the authentication for remote access, remote electronic payments and any other actions through a remote channel which may imply a risk of payment fraud or other abuses has failed to generate an authentication code for the purposes of paragraph 1, it shall not be possible to identify which of the elements referred to in that paragraph was incorrect; (b) the number of failed authentication attempts that can take place consecutively, after which the actions referred to in Article 97(1) of Directive (EU) 2015/2366 shall be temporarily or permanently blocked, shall not exceed five within a given period of time; (c) the communication sessions are protected against the capture of authentication data transmitted during the authentication and against manipulation by unauthorised parties in accordance with the requirements in Chapter V; (d) the maximum time without activity by the payer after being authenticated for accessing its payment account online shall not exceed 5 minutes. | Most of these are achieved via FIDO; see FIDO-PSD2. For additional information on timeouts in SPC, see [issue 67](https://github.com/w3c/secure-payment-confirmation/issues/67).

#### [RTS Article 5] – Dynamic linking

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 5.1 | Where payment service providers apply strong customer authentication in accordance with Article 97(2) of Directive (EU) 2015/2366, in addition to the requirements of Article 4 of this Regulation, they shall also adopt security measures that meet each of the following requirements: (a) the payer is made aware of the amount of the payment transaction and of the payee; (b) the authentication code generated is specific to the amount of the payment transaction and the payee agreed to by the payer when initiating the transaction; (c) the authentication code accepted by the payment service provider corresponds to the original specific amount of the payment transaction and to the identity of the payee agreed to by the payer; (d) any change to the amount or the payee results in the invalidation of the authentication code generated. | Implementations of SPC include the secure display of the information required by 5.1.a. The SPC assertion includes a signature over a hash of transaction specific information (thus, amount and payee information) as well as a relying-party provided challenge; this fulfills 5.1.b. To fulfill 5.1.c and 5.1.d, the Relying Party needs to be made aware of the transaction amount and payee identity prior to the invocation of SPC; we anticipate this will be addressed via protocols such as EMV® 3-D Secure. Once SPC authentication has taken place, the Relying Party can use the (FIDO) public key generated during enrollment to validate the SPC assertion. If the signed data matches the Relying Parties expectations, the Relying Party is assured that the SPC implementation presented this data to user who then signed it with the user's Authenticator.
| Article 5.2 | For the purpose of paragraph 1, payment service providers shall adopt security measures which ensure the confidentiality, authenticity and integrity of each of the following: (a) the amount of the transaction and the payee throughout all of the phases of the authentication; (b) the information displayed to the payer throughout all of the phases of the authentication including the generation, transmission and use of the authentication code | SPC is a JavaScript API supported by the browser, which represents the user. Thus, for the purposes of 5.2.a and 5.2.b, calling SPC does not itself expose information to any other parties. For information about protections once the browser invokes FIDO authentication, see FIDO-PSD2. For the authenticity and integrity requirements, the SPC implementation displays information that it was provided as input. Once the SPC implementation displays native user experience, the information can no longer be tampered with in the merchant environment. Finally, the relying party retains the authoritative information about the transaction and enrolled instrument, and thus can validate the authentication results to ensure the authenticity and integrity of the data.
| Articles 6/7/8 | 1. Payment service providers shall adopt measures to mitigate the risk that the elements of strong customer authentication categorised as knowledge/possession/ inherence are uncovered by, or used by or disclosed to, unauthorised parties. 2. The use by the payer of those elements shall be subject to mitigation measures in order to prevent their disclosure to unauthorised parties or their replication or their unauthorised use. 3. Payment service providers shall ensure that access devices and software that read authentication elements categorised as inherence have a very low probability of an unauthorised party being authenticated as the payer. | This is achieved via FIDO; see FIDO-PSD2.
| Article 9 (1) | Payment service providers shall ensure that the use of the elements of strong customer authentication referred to in Articles 6, 7 and 8 is subject to measures which ensure that, in terms of technology, algorithms and parameters, the breach of one of the elements does not compromise the reliability of the other elements. | This is achieved via FIDO; see FIDO-PSD2.
| Articles 9.2 & 9.3 | Payment service providers shall adopt security measures, where any of the elements of strong customer authentication or the authentication code itself is used through a multi-purpose device, to mitigate the risk which would result from that multi-purpose device being compromised. For the purposes of paragraph 2, the mitigating measures shall include each of the following: (a) the use of separated secure execution environments through the software installed inside the multi-purpose device; (b) mechanisms to ensure that the software or device has not been altered by the payer or by a third party; (c) where alterations have taken place, mechanisms to mitigate the consequences thereof. | This is achieved via FIDO; see FIDO-PSD2.

### [RTS Chapter IV] Confidentiality and integrity of the Payment Service User’s security credentials

#### [RTS Article 22] – General Requirements

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Articles 22.1 & 22.2 | Payment service providers shall ensure the confidentiality and integrity of the personalised security credentials of the payment service user, including authentication codes, during all phases of the authentication. 2. For the purpose of paragraph 1, payment service providers shall ensure that each of the following requirements is met: (a) personalised security credentials are masked when displayed and are not readable in their full extent when input by the payment service user during the authentication; (b) personalised security credentials in data format, as well as cryptographic materials related to the encryption of the personalised security credentials are not stored in plain text; (c) secret cryptographic material is protected from unauthorised disclosure. | This is primarily achieved via FIDO; see FIDO-PSD2. Note that the SPC authentication confirmation dialog displays whatever is input by the caller. Thus, the caller should provide just enough instrument information for user recognition of the instrument.
| Article 22.3 | Payment service providers shall fully document the process related to the management of cryptographic material used to encrypt or otherwise render unreadable the personalised security credentials. | This is achieved via FIDO; see FIDO-PSD2.
| Article 22.4 | Payment service providers shall ensure that the processing and routing of personalised security credentials and of the authentication codes generated in accordance with Chapter II take place in secure environments in accordance with strong and widely recognised industry standards. | This is achieved via FIDO; see FIDO-PSD2.

#### [RTS Article 23] – Creation and transmission of credentials

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 23 | Payment service providers shall ensure that the creation of personalised security credentials is performed in a secure environment. They shall mitigate the risks of unauthorised use of the personalised security credentials and of the authentication devices and software following their loss, theft or copying before their delivery to the payer. | This is achieved via FIDO; see FIDO-PSD2.

#### [RTS Article 24] – Association with the payment service user

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Articles 24.1 & 24.2 | 1. Payment service providers shall ensure that only the payment service user is associated, in a secure manner, with the personalised security credentials, the authentication devices and the software. 2. For the purpose of paragraph 1, payment service providers shall ensure that each of the following requirements is met: (a) the association of the payment service user’s identity with personalised security credentials, authentication devices and software is carried out in secure environments under the payment service provider’s responsibility comprising at least the payment service provider’s premises, the internet environment provided by the payment service provider or other similar secure websites used by the payment service provider and its automated teller machine services, and taking into account risks associated with devices and underlying components used during the association process that are not under the responsibility of the payment service provider; (b) the association by means of a remote channel of the payment service user’s identity with the personalised security credentials and with authentication devices or software is performed using strong customer authentication. | See FIDO-PSD2 for the parts of this requirement addressed by FIDO; SPC does not add to this.

#### [RTS Article 25] – Delivery of credentials, authentication devices and software

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Articles 25.1 & 25.2 | 1. Payment service providers shall ensure that the delivery of personalised security credentials, authentication devices and software to the payment service user is carried out in a secure manner designed to address the risks related to their unauthorised use due to their loss, theft or copying. 2. For the purpose of paragraph 1, payment service providers shall at least apply each of the following measures: (a) effective and secure delivery mechanisms ensuring that the personalized security credentials, authentication devices and software are delivered to the legitimate payment service user; (b) mechanisms that allow the payment service provider to verify the authenticity of the authentication software delivered to the payment services user by means of the internet; (c) arrangements ensuring that, where the delivery of personalised security credentials is executed outside the premises of the payment service provider or through a remote channel: (i) no unauthorised party can obtain more than one feature of the personalised security credentials, the authentication devices or software when delivered through the same channel; (ii) the delivered personalised security credentials, authentication devices or software require activation before usage; (d) arrangements ensuring that, in cases where the personalised security credentials, the authentication devices or software have to be activated before their first use, the activation shall take place in a secure environment in accordance with the association procedures referred to in Article 24. | This is achieved via FIDO; see FIDO-PSD2.

#### [RTS Article 26] – Renewal of personalized security credentials

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 26 | Payment service providers shall ensure that the renewal or reactivation of personalized security credentials adhere to the procedures for the creation, association and delivery of the credentials and of the authentication devices in accordance with Articles 23, 24 and 25. | This is achieved via FIDO; see FIDO-PSD2.

#### [RTS Article 27] – Destruction, deactivation and revocation

| Article     | Requirement | How SPC Meets It |
| ----------- | ----------- | ----------- |
| Article 27 | Payment service providers shall ensure that they have effective processes in place to apply each of the following security measures: (a) the secure destruction, deactivation or revocation of the personalised security credentials, authentication devices and software; (b) where the payment service provider distributes reusable authentication devices and software, the secure re-use of a device or software is established, documented and implemented before making it available to another payment services user; (c) the deactivation or revocation of information related to personalised security credentials stored in the payment service provider’s systems and databases and, where relevant, in public repositories.  | This is achieved via FIDO; see FIDO-PSD2. In addition, for any information stored in the browser related to an SPC implementation, browsers provide tools for lifecycle management (including deletion) of credentials.

## FAQ

### Can SPC be used with delegated authentication?

[As mentioned above](#scale), one design goal of SPC is to scale across
merchants. The general SPC flow emphasizes this point: the user
enrolls for SPC authentication once (e.g., with the issuing bank) and
can then authenticate on any merchant site without re-enrolling.

SPC can also be used in "delegated authentication" use cases, where
the merchant (or their payment service provider) is the Relying
Party. When the merchant or payment service provider, is the Relying
Party:

* The "native browser UX" and "cryptographic evidence" benefits of SPC
  remain relevant, but there is likely to be less "scalability across
  merchants."

* Other parties in the payment ecosystem (e.g., the ACS in EMV® 3-D
  Secure) may not be in a position to directly validate the
  authentication results, but the authentication may still be useful
  as part of a risk assessment strategy. As an example of such a
  strategy, see [Technical Note: FIDO Authentication and EMV 3-D
  Secure – Using FIDO for Payment
  Authentication](https://fidoalliance.org/technical-note-fido-authentication-and-emv-3-d-secure-using-fido-for-payment-authentication/) by the FIDO Alliance and [Use of FIDO Data in 3-D Secure Messages](https://www.emvco.com/wp-content/uploads/documents/EMVCo_3DS_FIDOData-WPv1.0_20200710.pdf) by EMVCo.

### How can one test and evaluate an SPC-based solution (in light of Article 3)?

An SPC-based solution consists of the following components:

* The FIDO Authenticator
* The browser
* The PSP application/environment
* The means used to exchange credentials and assertions (e.g., via EMV® 3-D
  Secure or a different protocol or proprietary channels).
* The network connection

Independent labs can evaluate the code, documentation, test suites, third-party tools, and certification processes associated with these components. For FIDO authenticators, please see the [FIDO Alliance's certification process](https://fidoalliance.org/certification/).

For the browser component specifically, we recommend the following:

* Start with specifications for specific features, including [Secure Payment Confirmation](https://w3c.github.io/secure-payment-confirmation/), [Web Authentication](https://w3c.github.io/webauthn/), and [Client to Authenticator Protocol (CTAP)](https://fidoalliance.org/specifications/download/). The specifications are important to understanding which aspects of underlying code are relevant to a security evaluation. Specifications generally include "Security Considerations" sections.
* The W3C community develops [publicly available test suites](http://web-platform-tests.org/) to promote interoperable and correct implementations of its specifications. Independent labs may validate expected browser behavior using these test suites.
* To evaluate an individual browser, we recommend contacting the browser vendor and locating testing and good practices resources the vendor may provide for this purpose. In most cases, browser engine code is available as an open source project that can evaluated independently.

Regarding the integrity and security of SPC communications, please note
the following:

* The Relying Party remains the authoritative source of information
about the user's payment instruments and authentication credentials. A
Relying Party can detect bad data upon verification of an SPC
credential and refuse to authorize a transaction.
* Because browsers are used for significant sensitive and high-value
commercial activity, there is substantial and adversarial testing of
browser security. End-users can more easily trust their browser than
they can trust or assess the security of multiple third-party
applications. Furthermore, the browser can help protect both users and
those with whom they do business from risks of spoofing and
compromise. For example, SPC moves the display of transaction
information into a more trusted environment than code running in the
browser (see [SPC-SA-1](#spc-sa-1) and [SPC-SA-2](#spc-sa-2)), to help
reduce the risk of spoofing.
