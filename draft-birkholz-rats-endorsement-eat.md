---
title: A CWT Claims Set Definition for RATS Endorsement Tokens
abbrev: Endorsement EAT
docname: draft-birkholz-rats-endorsement-eat-latest
stand_alone: true
ipr: trust200902
area: Security
wg: RATS Working Group
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
- ins: M. Eckel
  name: Michael Eckel
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: michael.eckel@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany

normative:
  RFC2119:
  RFC8174:
  RFC7519: jwt
  RFC8610: cddl
  RFC8392: cwt
  IANA.cwt: claims

informative:
  I-D.ietf-rats-architecture: rats-arch
  I-D.ietf-rats-eat: eat
  TCGGLOSS:
    target: https://trustedcomputinggroup.org/wp-content/uploads/TCG-Glossary-V1.1-Rev-1.0.pdf
    title: TCG Glossary Version 1.1 Revision 1.00
    author:
    - org: TCG
    date: 2017-05-11
  IEEE.RA:
    title: IEEE Registration Authority
    target: https://standards.ieee.org/products-services/regauth/index.html
    date: false

--- abstract

An Endorsement is defined by the RATS Architecture as a "secure statement that some entity (typically a manufacturer) vouches for the integrity of an Attester's signing capability". This documents defines Claims to be used in CBOR Web Tokens in the same fashion attestation Evidence can be represented via Entity Attestation Tokens (EAT). The defined Claims can be included in Endorsement Tokens. Endorsement Tokens can be provided by a manufacturer or a third party authority to vouch for the capabilities and characteristics of a hardware component a RATS Attester is not capable to create Evidence about.

--- middle

# Introduction

Remote ATtestation procedureS (RATS) can be used to establish trust in the trustworthiness of a remote peer (the Attester). As a Relying Party typically cannot evaluate every kind of Attester by itself, the RATS architecture {{-rats-arch}} defines the Verifier role, to off-load the burden of appraisal to another entity than the Relying Party itself. The duty of a Verifier is to produce Attestation Results that are then easier to digest by a Relying Party in comparison to Evidence that can potentially be both large and/or esoteric for a generic Relying Party. Evidence are believable Claims about the Attester. Next to Evidence, a Verifier requires Endorsements. Endorsements are signed documents that include Claims about components of an Attester that an Attester cannot create Evidence about. Very prominent examples are Roots of Trust, such as a Static Code Root of Trust for Measurement as defined in the Trusted Computing Group (TCG) Glossary {{TCGGLOSS}}. These Endorsements of components of a composite device are typically provided by their manufacturer, a corresponding supply chain entity that assembles a composite device, or a certification authority.

This documents defines CBOR Web Token (CWT, {{-cwt}}) Claims that can be assembled into a CWT Claims Set to compose Endorsement Tokens. This is done in the same fashion as Claims are assembled into Entity Attestation Tokens {{-eat}} that can represent, for example, attestation Evidence for RATS.

## Terminology

This document uses the terms Claims, Claims Set, and CBOR Web Token Claims set as defined in {{-cwt}}.

{::boilerplate bcp14}

# Endorsement Claims Definition

This section uses the same definition style for Claims as introduced in {{-eat}}. New Claims to be used in Endorsement Tokens are specified below. A JSON Web Token Claims (JWT, {{-jwt}}) definition is out-of-scope of this document. Corresponding Claims are (to be) registered in the 'CWT Claims' subregistry of {{-claims}}.

Each Claim definition is accompanied by a value definition using the Concise Data Definition Language (CDDL, {{-cddl}}). An Endorsement Token that is using Claims that are defined in this document MUST include Claim values as specified in this document.

Note to the reader: in this -00 I-D there are only five exemplary Claims included yet. This list is far from complete or polished.

## Component Manufacturer Claim

As a fall-back alternative to the more specific oemid Claim defined in {{-eat}}, this Claim allows for byte strings representing entity identifiers that are not based on IEEE MA-L, MA-M, MA-S or an IEEE CID {{IEEE.RA}}.

~~~~CDDL
manufacturer-endorsement-claim = (
  manufacturer-endorsement => bytes,
)
~~~~

## Component Version Claim

A byte string representing a firmware version of a hardware component. Potentially, the value is derived from multiple version numbers, such as major and minor version number. The version represents the hardware component at the time the Endorsement Token was created, typically during manufacturing.

~~~~CDDL
version-endorsement-claim = (
  version-endorsement => bytes,
)
~~~~

## Component Model Claim

A manufacturer-specific byte string that represents the part number or a similar model identifier as defined by the manufacturer.

~~~~CDDL
model-endorsement-claim = (
  model-endorsement => bytes,
)
~~~~

## Field Upgradable Claim

A Claim that indicates if the firmware of a hardware component is mutable and therefore can be updated after manufacturing or not.

~~~~CDDL
field-upgradable-claim = (
  field-upgradable => bool,
)
~~~~

## Shielded Secret Origination Claim

An indicator that shows if a securely stored secret key in the hardware component was generated by a function internal to the hardware component or if the secret key was enrolled in a secure and controlled environment by the manufacturer.

~~~~CDDL
secret-origination-claim = (
  secret-origination => internal / external
)

internal = 0
external = 1
~~~~

## Common Criteria Claim

A reference to the specification document that includes evaluation results and parameters as defined by Common Criteria. This Claim value is a composite of an URI pointing to the specification document as well as a hash value specification document to ensure its authenticity. The hash entry is a composite of an algorithm ID as defined by the IANA "Named Information Hash Algorithm Registry" and the hash value as a byte string.

~~~~CDDL
common-criteria-claim = (
  common-criteria => [ any-uri,
                       hash,
                     ]
)

any-uri = text

hash = [ hash-alg-id: int,
         hash-value: bytes,
       ]
~~~~

# Privacy Considerations

Potentially

# Security Considerations

Most likely a sub-set of the trust relationships corresponding to the RATS architecture

# IANA Considerations

In this section the Claim registration in {{-claims}} for the corresponding Claim definition above will be elaborated on.

--- back
