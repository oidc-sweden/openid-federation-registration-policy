%%%
title = "OpenID Federation Registration Policy 1.0 - draft 00"
abbrev = "openid-federation-registration-policy"
ipr = "none"
workgroup = "OpenID Connect A/B"
keyword = ["security", "openid", "ssi"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-federation-registration-policy"
status = "standard"

[[author]]
initials="M."
surname="Lindström"
fullname="Martin Lindström"
organization="IDsec Solutions"
    [author.address]
    email = "martin@idsec.se"

[[author]]
initials="S."
surname="Santesson"
fullname="Stefan Santesson"
organization="IDsec Solutions"
    [author.address]
    email = "stefan@idsec.se"

%%%

.# Abstract

This specification defines an extension to OpenID Federation that extends the JWT Claims Set of Subordinate Statements by specifying the `registration_policy` Claim. This Claim is intended to hold identifiers for one or more registration policies that were applied when registering the subject of a Subordinate Statement as a trusted Federation Entity.

{mainmatter}

# Introduction

When a Federation Entity is made available under either a Trust Anchor or an Intermediate Entity, the issuance of a Subordinate Statement for this Entity extends trust to the subject Entity from the issuer of the statement.

This extension of trust implied by the issuance of a Subordinate Statement is not well defined within OpenID Federation. The basic structure implies that a Leaf Entity is responsible for declaring its own metadata, and that authorizations granted by third parties are provided via Trust Marks. The following aspects of the Leaf Entity are, however, always vouched for by the Immediate Superior Entity issuing the Subordinate Statement:

- The binding of a federation key to an Entity Identifier.

- That the Entity holding the federation key is the legitimate Entity representing this Entity Identifier.

- That the Entity holding the federation key is a legitimate member of the community implied by membership within a particular federation and can be trusted as such.

The last item could be addressed using Trust Marks, but some legitimacy of services may be based on membership alone.

This specification extends the function of Subordinate Statements and allows the issuer to include one or more registration policy identifiers. Each registration policy identifier represents a set of rules and procedures followed by the issuer before issuing a Subordinate Statement for the subject Entity. This may include, but is not limited to:

- Procedures for establishing the real legal Entity behind the Entity Identifier.

- Procedures for establishing the authorization of a physical person to represent the legal Entity behind the Entity Identifier.

- Procedures for establishing the real registered identity and address of the legal Entity behind the Entity Identifier.

- Procedures for establishing the ownership of the domain used in URLs of identifiers and endpoints used by the subject Entity.

Superior Entities such as Trust Anchors and Intermediate Entities MAY use registration policies as a constraining mechanism to exclude validation of Trust Chains that do not meet certain registration policy requirements, by using the `constraints` claim of a Subordinate Statement, see  (#registration_policy_constraints).

## Requirements Notation and Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in
this document are to be interpreted as described in BCP 14 [@!RFC2119]
[@!RFC8174] when, and only when, they appear in all capitals, as shown here.

## Terminology

This specification uses the terms "Claim", "JSON Web Token (JWT)", and "JWT Claims Set" as defined by JSON Web Token (JWT) [@!RFC7519], and "Entity", "Entity Identifier", "Trust Anchor", "Federation Entity", "Entity Statement", "Entity Configuration", "Subordinate Statement", "Intermediate Entity", "Leaf Entity", "Subordinate Entity", "Superior Entity", "Immediate Superior Entity", and "Trust Chain" defined in OpenID Federation 1.0 [@!OpenID.Federation].

# The registration\_policy Claim {#the_registration_policy_claim}

This section defines the `registration_policy` Claim that MAY be included in a Subordinate Statement.

The `registration_policy` Claim holds an array of string values, each specifying the URI identifier of a registration policy that was applied by the issuer of the Subordinate Statement during registration of 
the subject as a trusted Subordinate Entity.

The `registration_policy` Claim MUST NOT be included in an Entity Configuration.

Examples:

```json=
"registration_policy" : [ "https://example.com/policy/verified" ],
...
```

```json=
"registration_policy" : [ "https://example.com/policy/swedenconnect",
                          "https://example.com/policy/swamid" ],
...
```

# Registration Policy Constraints {#registration_policy_constraints}

Section 6.2, "Constraints", of [@!OpenID.Federation] is extended with the following parameter:

`registration_policy`
: <br>OPTIONAL. A JSON object that specifies restrictions on registration policies provided in `registration_policy` Claims in Subordinate Statements within a Trust Chain.

Restrictions are expressed in terms of registration policy URI identifiers. They are defined using the `required` and/or `prohibited` members of the `registration_policy` object. Each member contains an array of URIs that are either required or prohibited.

The following constraint rules apply:

- If a Subordinate Statement contains a `registration_policy` Claim with one or more URIs that appear in the `prohibited` array, the constraint check fails.

- If the `required` array is non-empty, the constraint check succeeds if, and only if, the Subordinate Statement contains at least one URI that matches a value in the `required` array.


```json=
{
  "registration_policy" : {
    "required" : [
      "https://example.com/policy/verified",
      "https://example.com/policy/secure"
    ],
    "prohibited" : [
      "https://example.com/policy/open"
    ]
  }
}
```

**Example**: A constraint for registration policies stating that at least one of the policies `https://example.com/policy/verified` or `https://example.com/policy/secure` must appear as values to a Subordinate Statement's `registration_policy` Claim, and that the `https://example.com/policy/open` must not appear.

# Acknowledgments

We would like to thank the following individuals for their comments, ideas, and contributions to this implementation profile and to the initial set of implementations.

- Leif Johansson, Siros

{backmatter}

<reference anchor="OpenID.Core" target="http://openid.net/specs/openid-connect-core-1_0.html">
  <front>
    <title>OpenID Connect Core 1.0 incorporating errata set 2</title>
    <author initials="N." surname="Sakimura" fullname="Nat Sakimura">
      <organization>NRI</organization>
    </author>
    <author initials="J." surname="Bradley" fullname="John Bradley">
      <organization>Ping Identity</organization>
    </author>
    <author initials="M." surname="Jones" fullname="Michael B. Jones">
      <organization>Microsoft</organization>
    </author>
    <author initials="B." surname="de Medeiros" fullname="Breno de Medeiros">
      <organization>Google</organization>
    </author>
    <author initials="C." surname="Mortimore" fullname="Chuck Mortimore">
      <organization>Salesforce</organization>
    </author>
   <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID.Federation" target="https://openid.net/specs/openid-federation-1_0.html">
        <front>
          <title>OpenID Federation 1.0</title>
		  <author fullname="R. Hedberg, Ed.">
            <organization>independent</organization>
          </author>
          <author fullname="Michael B. Jones">
            <organization>Self-Issued Consulting</organization>
          </author>
          <author fullname="A. Solberg">
            <organization>Sikt</organization>
          </author>
          <author fullname="John Bradley">
            <organization>Yubico</organization>
          </author>
          <author fullname="Giuseppe De Marco">
            <organization>independent</organization>
          </author>
          <author fullname="Vladimir Dzhuvinov">
            <organization>Connect2id</organization>
          </author>
          <date day="15" month="October" year="2025"/>
        </front>
</reference>

# Notices

Copyright (c) 2026 The OpenID Foundation.

The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that MAY cover technology that MAY be required to practice this specification.

# Document History

   [[ To be removed from the final specification ]]

   -00 

   *  Initial version
