---
title: "Problem Statement for a Digital Emblem"
abbrev: "PS-DE"
category: info

docname: draft-linker-digital-emblem-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: TBD
keyword:
venue:

author:
 -
    fullname: Felix Linker
    organization: ETH Zurich
    email: flinker@inf.ethz.ch
 -
    fullname: David Basin
    organization: ETH Zurich
    email: flinker@inf.ethz.ch
 -
    fullname: Mauro Vignati
    organization: ICRC
    email: mvignati@icrc.org
 -
    fullname: Tommy Jensen
    organization: Microsoft
    email: tojens@microsoft.com

normative:

informative:
   ADEM:
      target: https://dl.acm.org/doi/10.1145/3576915.3616578
      title: "ADEM: An Authentic Digital EMblem"
      author:
         -
            name: Felix Linker
            org: Department of Computer Science, ETH Zurich
         -
            name: David Basin
            org: Department of Computer Science, ETH Zurich
      date: November 2023
      seriesinfo:
         CCS '23: Proceedings of the 2023 ACM SIGSAC Conference on Computer and Communications Security
   ADEM-SPEC:
      target: https://adem-wg.github.io/adem-spec/draft-adem-wg-adem-core.html
      title: An Authenticated Digital EMblem - Core Specification
      author:
         -
            name: Felix Linker
            org: ETH Zurich
         -
            name: Dennis Jackson
            org: None
         -
            name: David Basin
            org: ETH Zurich
      date: 28 August 2023
   DE-REPORT:
      target: https://www.icrc.org/en/document/icrc-digital-emblems-report
      title: "Digitalizing the Red Cross, Red Crescent and Red Crystal Emblems"
      author:
         -
            name: Tilman Rodenhäuser
            org: ICRC
         -
            name: Mauro Vignati
            org: ICRC
         -
            name: Larry Maybee
            org: Australian Red Cross
         -
            name: Hollie Johnston
            org: Australian Red Cross
      date: November 2022
      format:
         PDF: https://www.icrc.org/en/download/file/253888/icrc_digitalizing_the_rcrc_emblem.pdf


--- abstract

In times of armed conflict, the protective emblems of the red cross, red crescent, and red crystal are used to mark *physical* assets.
This enables military units to identify assets as respected and protected under international humanitarian law.
This draft explores how one could apply the protective emblems to *digital*, network-connected infrastructure using a *digital emblem*, and defines the requirements of a digital emblem, emphasizing security requirements.

Notably, a digital emblem has a unique combination of security requirements, namely, authentication, accountability, and a property that we call *covert inspection*.
Covert inspection means that those wishing to authenticate assets as protected must be able to do so without revealing that they may attack unprotected entities.

--- middle

# Introduction

International Humanitarian Law (IHL) mandates that military units must not attack medical facilities, such as hospitals.
The emblems of the red cross, red crescent, and red crystal are used to mark *physical* infrastructure (e.g., by a red cross painted on a hospital's rooftop), thereby enabling military units to identify those assets as protected under IHL.
The International Committee of the Red Cross (ICRC) recently posed the question: How can one extend such markings to *digital* infrastructure such as servers and networks? {{DE-REPORT}}

The goal of a digital emblem is to prevent cyberattacks on humanitarian infrastructure.
Parties to armed conflicts are bound by IHL, and are thus required by law to respect the protective emblems.
Other actors may not be bound by IHL, but could still respect a digital emblem.
Either out of respect for the humanitarian cause or to avoid unwanted attention when attacking marked assets.
A digital emblem can only serve this purpose, though, if it meets a unique combination of requirements.

1. Digital emblems require authentication as assets must not be able to fake protection, for example, by transferring emblems from medical to military infrastructures.
2. Protective emblems must be decentralized, i.e., there must be no central authorities that govern the use or distribution of digital emblems.
Under IHL, states themselves determine which parties and assets may display the emblem under their authority.
3. Systems with a decentralized trust model are prone to misuse.
Therefore, a digital emblem must be designed so that parties can be held accountable whenever they mark unprotected infrastructure.
Protection under IHL stems from law, and laws must be enforceable to have an effect.
4. Those wishing to authenticate assets must be able to verify protective emblems in a way that does not call attention to the fact that they are screening potential targets.
We call this property *covert inspection*.
This is analogous to looking at a physical emblem from a distance: A flag of a red cross does similarly not raise attention to the fact that its being looked at.

Work on the digital emblem dates back multiple years.
In 2020, the ICRC invited two research institutions, Johns Hopkins University Applied Physics Laboratory (APL) and the Centre for Cyber Trust (CECYT), a joint research centre between ETH Zurich and Bonn University, to develop technical proposals for a digital emblem.
These proposals were presented to and evaluated by a group of international experts at the invitation of the ICRC {{DE-REPORT}}.
We discuss the proposed designs in {{proposals}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# The Problem

## Legal and Historical Background

The Geneva Conventions and their *Additional Protocols* (APs) constitute the core of IHL and establish legal rules on the conduct of armed conflict.
These Conventions and Protocol establish the meaning and usage of protective emblems, namely, the red cross, crescent, and crystal.

The emblems have a protective and an indicative function.
In its protective function, parties to conflict may apply an emblem to assets that exclusively undertake medical functions, such as vehicles, personnel, or buildings.
These emblems inform other parties that they must not be attacked.
In its indicative function, an emblem signals affiliation with the International Red Cross and Red Crescent Movement.

Since 1949, the Geneva Conventions have been revised.
AP I contains additional regulations on how parties to conflict can communicate their status using technical means, like radar transponders and radio signals.
Recognizing that technology may progress rapidly, the amendment process for AP I is streamlined.
For example, this process could begin through expert consultations convened by the ICRC.

## Problem Domain

The concept of a digital emblem touches a variety of stakeholders.
First and foremost, there are *emblem issuers (EIs)*, groups that provide medical services or conduct humanitarian operations such as military forces or organizations like Doctors Without Borders.
As part of their activities, they may display the protective emblems on their *protected digital assets*, such as mobile devices (tablets, smartphones), servers (both virtual and dedicated), or an intranet.
Prior to commencing their operations, EIs must seek permission from competent *authorities*.
When they are given permission, EIs can apply digital emblems to their protected assets, which *present* them to three types of *agents*.

*Regular users* of an asset do not pay attention to the emblem, for example, when they visit a website.
*Verifiers* pay attention to the emblem and only wish to attack lawful (under IHL) targets.
They are typically part of an armed force engaged in a conflict.
We can assume that most verifiers (typically military units) will be associated with an authority (typically their nation state or an ally), and, hence, that such verifiers can obtain the authentic public keys of their affiliated authorities through secure, out-of-band channels.
Finally, we define *adversaries* as those agents that are willing to violate IHL and search to abuse digital emblems.
For example, they may seek to issue emblems to non-protected assets.

## Requirements {#reqs}

The digital emblem's requirements were adapted from the ICRC's report on digital emblems {{DE-REPORT}}.
However, whereas the report introduces requirements on a much higher, abstract level and without a detailed consideration of security, we present a comprehensive list of actionable, technical requirements.

The purpose of a digital emblem is to prevent attacks on protected assets by informing other parties about their status under IHL.
(Note that IHL also permits the indicative use of an emblem, we subsume this case under the protective use of an emblem for clarity.)
The digital emblem's requirements are derived from two important insights.
(i) A digital emblem critically requires adoption by verifiers, which (by definition) intend to attack assets not protected under IHL.
(ii) A digital emblem should comply with existing IHL, which facilitates the diplomatic process of adopting a digital emblem.

### Covert Inspection

A digital emblem MUST NOT reveal who is inspecting it.
We call this requirement *covert inspection*.
If verifiers were to reveal that they are inspecting digital emblems, their targets (potentially unprotected) could use that knowledge to protect themselves against an imminent attack, and verifiers would therefore not inspect emblems.
In particular, covert inspection implies that emblem presentation must be *active*, i.e., verifiers will be sent emblems and need not query them explicitly.

### Authentic

Digital emblems MUST be *authentic*, i.e., a digital emblem only marks assets that are used to provide medical services or conduct humanitarian operations.
If it were not authentic, verifiers would risk that their lawful, i.e., unprotected, targets could avert attacks by displaying fraudulent emblems, and verifiers would again not inspect emblems.

### Decentralized Trust Model

Compliance with existing IHL implies that there MUST NOT be a fixed set of authorities that can regulate how a digital emblem is used, i.e., it must follow a *decentralized trust model*.

### Accountable Display

Systems with a decentralized trust model can suffer from misuse.
Should a digital emblem be codified in law, it is crucial that any unlawful display of digital emblems can be provably attributed to the respective parties such that they can be prosecuted.
Hence, a digital emblem MUST provide *accountability*.

### Removable & Verifiable Presence

A digital emblem should work just as the physical emblems.
Just as a flag with a red cross can be displayed and removed at any time, a digital emblem MUST be applicable to the widest possible range of use cases and digital technologies, and also be easily *removable*.
Additionally, agents MUST be able to *verify the presence* of digital emblems.
With physical emblems, unprotected assets will simply not show the red cross.
Likewise, in the digital domain, these assets will not present an invalid emblem, but rather no emblem, and verifiers must be able to determine when no emblem was shown to them.

## Use-Case Scenarios

In the following, we list a number of use cases that a digital emblem should cover.
These use cases were derived in collaboration with the ICRC and a broad range of international experts, including the medical sector, the information technology sector, the military sector, and cybersecurity experts.

It may turn out that there cannot be a single design proposal that covers all use-cases.
Nevertheless, the number of ways in which a digital emblem can be distributed should be kept minimal.
The more interfaces supported by a digital emblem, the greater the burden on verifiers, who would need to check every interface to ensure that they are not attacking a protected asset.

### Personal Devices

A digital emblem should apply to general purpose, personal computing devices such as laptops, smartphones, and tablets.
Typically, such devices have no stable IP address, but have a powerful operating system and support complex applications well.

### Constrained Devices

A digital emblem should apply to network-connected devices that are constrained in computing power, bandwidth, or cannot be easily modified, for example, medical or IoT devices.
Typically, such devices are deployed in a fixed environment, however, due to power, hardware, or legal constraints, they cannot support complex applications, or their software might not be modifiable at all.

### Servers

A digital emblem should apply to dedicated and virtual servers, e.g., web or database servers.
Such servers may or may not have a stable IP or a domain name associated with them.

### Networks

A digital emblem should apply to networks that are controlled by one organization, e.g., the ICRC's internal network.
Typically, such networks have an IP range associated with them.
Each device connected to this network should fall into one of the categories above and could thus be marked individually.
However, marking entire networks should drastically ease the burden of deployment, verification, and distribution.

## Excluded Scenarios

Notably, a digital emblem cannot be applied to infrastructure that is used for both services worthy and unworthy of protection.
A digital emblem must always identify assets that only serve purposes that enjoy protection under IHL.

# Proposed Designs for a Digital Emblem {#proposals}

Two designs were proposed to solve the problem of a digital emblem.
First, JHU APL proposed a DNS-based solution for a digital emblem {{?I-D.haberman-digital-emblem}}.
Second, CECYT (a joint endeavour between ETH Zurich and Bonn University) proposed *An Authentic Digital EMblem (ADEM)*, which was initially described in an academic publication {{ADEM}}, but was also specified technically {{ADEM-SPEC}} and has openly accessible, working prototypes ([Go library and CLI](https://github.com/adem-wg/adem-proto), [Browser extension](https://github.com/adem-wg/adem-chrome)).
We focus here on ADEM as it was selected by the ICRC as the most suitable proposal for a digital emblem given its scope of applying emblems to digital assets and using the existing physical emblems for labeling physical assets.

ADEM was designed following the requirements laid out in this draft.
ADEM follows a decentralized trust model and utilizes existing infrastructure wherever possible, e.g., it provides accountability through the Certificate Transparency infrastructure {{?RFC6962}}.
ADEM was designed to be distributed over many channels and as such is not bound to one mode of transportation.
The academic paper foresees distribution of digital emblems via TLS, DNS, and UDP.
Next to its prototype, ADEM's security was thoroughly evaluated and formal proofs of security are described in the academic publication.

Once there is consensus on the scope of a digital emblem, the plan is to propose ADEM as a basis for designing a digital emblem.
Although ADEM has gone through several iterations in collaboration with the ICRC, it is expected that it will be refined by the IETF WG that adopts it to ensure industry interoperability with good protocol practices.
In particular, it must be investigated whether it fits the needs of all stakeholders of a digital emblem.

# Considerations of Potential Risks

Because digital emblems label digital infrastructure as legally protected, misuse of a digital emblem may
not always be automatically detectable. In this section, we discuss two examples of potential misuse and
how the {{reqs}} section aims ensure that this standard is utilized responsibly.

First, a nation state could misuse a digital emblem to wrongfully protect infrastructure used to enforce
Internet censorship or shutdowns for its population. Second, technology vendors that provide online services
or resources might maliciously or accidentally apply digital emblems to not only IHL-protected assets but
their general infrastructure that hosts this and other services. In both cases, such misuse would abuse and
potentially undermine combatants' and resistance movements' respect for IHL only to label one's own
infrastructure, which even may actively harm human rights.

In all cases where a digital emblem is deployed, it should be noted that verifying a digital emblem
MAY include more than verifying its presence and authenticity. Verifiers MAY also observe other behavior
of the emblem-bearing asset, applying heuristics to check whether its plausible that the asset displays a
digital emblem. Additionally, we point out that digital emblem's mechanism of providing authentication
additionally enables combatants to hold emblem-bearers accountable should misuse be detected.

# Security Considerations

The requirements "covert inspection", "authentic", and "accountable display" are all security requirements, i.e., one must consider powerful adversaries trying to break these requirements when evaluating a proposal for a digital emblem.
The original, academic paper proposing *ADEM: An Authentic Digital Emblem* formally verified ADEM's security, and any subsequent proposal should be rigorously analyzed as well.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Parts of this draft are based on the initial academic publication describing the proposal *ADEM: An Authentic Digital EMblem* {{ADEM}}.
Initial work on this project was funded by the Werner Siemens-Stiftung (WSS).
We thank the WSS for their generous support.
