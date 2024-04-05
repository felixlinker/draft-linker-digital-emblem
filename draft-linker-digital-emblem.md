---
title: "An Authentic Digital Emblem"
abbrev: "ADEM"
category: info

docname: draft-linker-digital-emblem
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -
    fullname: Felix Linker
    organization: ETH Zurich
    email: flinker@inf.ethz.ch

normative:

informative:


--- abstract

In times of armed conflict, the emblems of the red cross, red crescent, and red crystal are used to mark *physical* infrastructure.
This enables military units to identify assets as protected under international humanitarian law to avoid attacking them.
This draft defines and tackles the novel security problem of how to extend such protection to *digital*, network-connected infrastructure through a digital emblem.
A digital emblem has a unique combination of security requirements, namely, authentication, accountability, and a property that we call *covert inspection*.
Covert inspection states that those wishing to authenticate assets as protected must be able to do so without revealing that they may attack unprotected entities.

This document defines the requirements of a digital emblem, emphasizing security requirements, and presents *ADEM*, a decentralized design that implements a digital emblem analogous to the physical emblems of the red cross, crescent, and crystal.

--- middle

# Introduction

International Humanitarian Law (IHL) mandates that military units must not target medical facilities, such as hospitals.
The emblems of the red cross, red crescent, and red crystal are used to mark *physical* infrastructure (e.g., by a red cross painted on a hospital's rooftop), thereby enabling military units to identify those assets as protected under IHL.
The International Committee of the Red Cross (ICRC) recently posed the question: How can one extend such markings to *digital* infrastructure such as servers and networks? <!-- TODO: Cite report -->

Extending protection to digital infrastructure promises to reduce cyber attacks on humanitarian infrastructure.
Although only state-actors are bound to IHL, thus required by law to respect the protective emblems, the hope is that also non-state-actors will respect a digital emblem.
Be it out of respect for the humanitarian cause, or be it to avoid unwanted attention.
A digital emblem can only fulfil this promise if it meets a unique combination of requirements.

1. Digital emblems require authentication as assets must not be able to fake protection, for example, by transferring markings from medical to military infrastructures.
2. Protective markings must be decentralized, i.e., there must be no central authorities that govern the use or distribution of digital emblems.
What complies with IHL is subject to debate, and different parties (possibly at war with one another) must be able to express conflicting views and must not need to trust the same parties.
3. The Web PKI has shown that a system of decentralized authorities is prone to misuse.
Therefore, a digital emblem must be designed so that one can hold misbehaving parties accountable whenever they mark unprotected infrastructure.
Protection under IHL stems from law, and laws must be enforceable to have an effect.
4. Those wishing to authenticate assets must be able to verify protective markings in a way that does not call attention to the fact that they are screening potential targets.
We call this property *covert inspection*.

We propose *An Authentic Digital Emblem* (ADEM), a design that solves all these challenges.
Digital emblems in ADEM are cryptographically signed messages and authenticate an asset as protected under IHL.
Emblems are distributed actively by these assets using ICMP and DNS in a way that provides covert inspection.
Emblems are accompanied by endorsements, certificate-like objects signed by independent authorities such as nation states.
(Note that we use the term 'endorsement' solely to disambiguate from Web PKI certificates.)
ADEM provides accountability through use of the Certificate Transparency (CT) log infrastructure, where parties commit to their public keys, and ADEM does not require any updates to the Internet's infrastructure.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# The Problem

We begin with the requirements for a digital emblem.
For this, we introduce relevant legal and historical background, describe the problem domain, and finally provide a digital emblem's requirements, which were adapted from the ICRC's report on digital emblems.
However, whereas the report introduces requirements on a much higher, abstract level and without a detailed consideration of security, we present a comprehensive list of actionable, technical requirements.

## Legal and Historical Background

The Geneva Conventions and their *additional protocols* (APs) constitute the core of IHL and establish legal rules on the conduct of armed conflict.
These rules codify the meaning and usage of protective emblems, namely, the red cross, crescent, and crystal, permitting *protected parties* (PPs) to mark their assets, such as vehicles, personnel, or buildings with these signs during armed conflicts.
These signs inform other parties about an asset's affiliation with the International Red Cross and Red Crescent Movement (*indicative* use), or about an asset's protected status under IHL (*protective* use).
Actors bound by IHL must respect an asset's protected status and not attack it, except in very limited circumstances.

Since 1949, the Geneva Conventions have been amended and extended.
AP I, for example, contains additional regulations on how PPs could communicate their status using technical means, like radar transponders and radio signals.
Recognizing that technology may progress rapidly, AP I allows for the ICRC to convene state experts to review and suggest updates to the technical means by which the PPs may be identified.
In order to initiate discussions among states, the ICRC proposed the idea of a digital emblem on an international stage. <!-- TODO: Cite report -->

## Problem Domain

The concept of a digital emblem touches a variety of stakeholders.
First and foremost, there are *protected parties* (PPs).
PPs operate in areas of conflict and conduct operations that enjoy protection under IHL utilizing *protected digital assets*, such as mobile devices (tablets, smartphones), servers (both virtual and dedicated), processes such as web servers, or a PP's intranet.
Prior to commencing their operations, PPs must seek permission from competent *authorities*.
When they are given permission, PPs can apply digital emblems to their protected assets, which *present* them to three types of *agents*.

*Regular users* of an asset do not pay attention to the emblem, for example, when they visit a website.
*Verifiers* pay attention to the emblem and only wish to attack lawful (under IHL) targets.
They are typically part of an armed force engaged in a conflict.
We can assume that most verifiers (typically military units) will be associated with an authority (typically their nation state or an ally), and, hence, that such verifiers can obtain the authentic public keys of their affiliated authorities through secure, out-of-band channels.
*Adversaries* are willing to violate IHL and search to abuse digital emblems.
For example, they may seek to issue emblems to non-protected assets.

## Requirements

The purpose of a digital emblem is to prevent attacks on protected assets by informing other parties about their status under IHL.
(Note that IHL also permits the indicative use of an emblem, we subsume this case under the protective use of an emblem for clarity.)
Therefore, a digital emblem can only prevent attacks by those who respect it.
There are various reasons why one may respect IHL.
Nation states or independent conflict parties may respect IHL because they are bound by it and, consequently, attacking protected assets can be a war crime.
Verifiers not bound by IHL, e.g., cybercriminals, may also want to not target protected assets to avoid unwanted attention or because they respect the humanitarian cause.
All verifiers, though, intend to attack assets not protected under IHL and, therefore, verifiers must be willing to pay attention to digital emblems for them to have an effect.
This has important implications on the problem statement.

In particular, verifiers never want to reveal themselves as inspecting emblems, which we express in a requirement that we call *covert inspection*.
If verifiers were to reveal this, their targets could use that knowledge to protect themselves against an imminent attack, and verifiers would not inspect emblems.
In particular, covert inspection implies that emblem presentation must be *active*, i.e., verifiers will be sent emblems and need not query them.

Moreover, we require that digital emblems must be *authentic*, i.e., a digital emblem only marks assets that *truly enjoy IHL protection*.
If it were not authentic, verifiers would risk that their lawful, i.e., unprotected, targets could avert attacks by displaying fraudulent emblems, and verifiers would again not inspect emblems.

Finally, a digital emblem should fit into the existing framework of IHL.
Compliance with IHL facilitates the diplomatic process of adopting a digital emblem, possibly integrating it into AP I.

Compliance with existing IHL implies that there must be no fixed set of authorities that can regulate how a digital emblem is used, i.e., it must be *decentralized*.
History, though, has shown that systems without central authorities (for example, the Web PKI) are prone to suffer from misbehaving authorities.
Should a digital emblem be codified in law, it is crucial that any illicit display of digital emblems can be prosecuted.
Hence, to account for misbehaving authorities, a digital emblem must provide *accountability*.

Moreover, compliance with existing IHL implies that a digital emblem should work similarly to physical emblems.
Just as a flag with a red cross can be affixed to and removed from supplies, personnel, buildings, or vehicles, a digital emblem must be applicable to the widest possible range of use cases and digital technologies, and also be easily removable.
Additionally, agents must be able to verify the presence of digital emblems.
With physical emblems, unprotected assets will simply not show the flag of the red cross.
Likewise, in the digital domain, these assets will not present an invalid emblem, but rather no emblem, and verifiers must be able to decide when no emblem was shown to them.

To summarize, we find that a digital emblem must meet the following three *security requirements*.

1. A digital emblem must be presented actively in a way that supports *covert inspection*: Agents who wish to verify whether an asset is protected under IHL must be indistinguishable from agents who interact with that asset for other purposes.

2. Emblems must be *verifiably authentic*.
Agents must be able to correctly associate emblems to the issuing PP and the respectively marked assets.

3. A digital emblem must provide *accountability*. Independent parties must be able to identify parties that issued fraudulent digital emblems.

Additionally, a digital emblem must meet the following *functional requirements* (FR).

1. A digital emblem must be *decentralized*.
There can neither be fixed parties in charge of distributing emblems nor a fixed set of authorities deciding who is eligible to issue emblems.

2. A digital emblem must be *widely applicable* to a broad range of devices, infrastructures, and organizations.

3. A digital emblem must be *removable*.

4. A digital emblem's *presence must be verifiable*.

## Potential Scenarios

- Personal devices (or more generally, general-purpose computing devices with regularly changing IP addresses)
- Servers that have a dedicated IP
- Networks
- Network-connected medical devices whose software cannot be modified

## Excluded Scenarios

- Dual-use infrastructure, e.g., in the Cloud

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Initial work on this project was funded by the Werner Siemens-Stiftung (WSS).
We thank the WSS for their generous support.