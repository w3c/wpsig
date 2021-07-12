# Using SPC to fulfill PSD2 Requirements for SCA and Dynamic Linking

Status: This is a draft document without consensus.

[Secure Payment Confirmation
(SPC)](https://github.com/w3c/secure-payment-confirmation) is a Web
API to support streamlined authentication during a payment
transaction. It is designed to scale authentication across merchants,
to be used within a wide range of authentication protocols, and to
produce cryptographic evidence that the user has confirmed transaction
details.

In this document we explain how SPC can be used to fulfill the Strong Customer Authentication (SCA) and Dynamic Linking requirements of Payment Services Directrive (PSD2).

## Background

W3C is working closely with the FIDO Alliance, EMVCo, and other partners to achieve a number of benefits through SPC:

* '''Authentication Streamlined for Payment'''. FIDO authentication provides a very good user experience. We expect SPC to build on that experience in several ways, by further accelerating authentication (compared to one-time passcodes), requiring fewer user gestures, offering a predictable user experience across sites, and avoiding redirects.
* '''Scalable and Ubiquitous'''. SPC supports streamlined authentication across multiple merchant sites following a single enrollment.
* Simpler and more Secure Front-end Deployment. The browser (or secure hardware) manages the display of the payment confirmation experience, removing the need for other parties (e.g., issuing banks or payment apps) to do so. In addition, enabling payment service providers or others to authenticate the user can reduce the need to embed code provided by a Relying Party in a Web page, reducing security risks. Reducing the need for redirects should also simplify solutions.
* '''Designed to Meet Regulatory Requirements'''. The standardized payment confirmation user experience is designed to help entities fulfill regulatory requirements (e.g., strong customer authentication and dynamic linking under PSD2) and other customer authentication use cases.

In this document we focus on the last point: how to use SPC to full
PDS2 requirements.

## Unique SPC Features

Note: For what follows, we recommend familiarity with <a href="https://fidoalliance.org/">FIDO Authentication</a> and specifically the <a href="https://www.w3.org/Webauthn/">Web Authentication specification</a>.

The above benefits are grounded in a small number of unique API features:

* Browser-native UX for payment confirmation. The browser (or secure hardware) provides a consistent and efficient authentication UX across merchant sites and relying parties.
* Cryptographic evidence. Payment confirmation generates cryptographic evidence of the user's confirmation of payment details. Note: This feature is expected to make use of FIDO protocols.
* Cross-origin authentication. With FIDO, the Relying Party that creates FIDO credentials is the only origin that can generate an assertion with those credentials to authenticate the user. With SPC, any origin can generate an assertion during a transaction even by leveraging another Relying Party's credentials.

## General SPC Flow

SPC consists of two phases: enrollment and authentication. The following flow diagram provides a general view of the parties involved in SPC Authentication.

<img src="spc-general.png" alt="General SPC Flow Diagram; PUML source available"/>

### SPC and Delegated Authentication

## SPC Used in Various Authentication Protocols

### EMV 3-D Secure

### EMV Secure Remote Commerce

### Open Banking

## Details of Using SPC to fulfill PSD2 Requirements

For details about the requirements, see [Regulatory Technical Standards on strong customer authentication and secure communication under PSD2 ](https://www.eba.europa.eu/sites/default/documents/files/documents/10180/1761863/314bd4d5-ccad-47f8-bb11-84933e863944/Final%20draft%20RTS%20on%20SCA%20and%20CSC%20under%20PSD2%20%28EBA-RTS-2017-02%29.pdf) (23 February 2017 edition).