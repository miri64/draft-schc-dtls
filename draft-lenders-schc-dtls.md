---
###
# Internet-Draft Markdown Template
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "SCHC Compression of DTLS Datagrams"
category: std

docname: draft-lenders-schc-dtls-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: INT
workgroup: SCHC
keyword:
 - Internet-Draft
 - SCHC
 - DTLS
venue:
  group: SCHC
  type: Working Group
  mail: schc@ietf.org
  arch: "https://mailarchive.ietf.org/arch/browse/schc/"
  github: "miri64/draft-schc-dtls"
  latest: "https://miri64.github.io/draft-schc-dtls/draft-lenders-schc-dtls.html"


author:
 -
    fullname: Martine Sophie Lenders
    organization: TU Dresden
    email: ietf@lenders.berlin

normative:
  RFC6347: dtls12
  RFC8724: schc
  RFC9147: dtls13
  RFC9846: tls13

informative:
  SCHC-DTLS-paper:
    seriesinfo:
      DOI: 10.1109/CSCN57023.2022.10051055
    title: 'DTLS Static Context Header Compression - Implementation and Evaluation in the Contiki-NG'
    author:
    - name: Alexandros Fragkiadakis
      ins: A. Fragkiadakis
      org: Institute of Computer Science, Foundation for Research and Technology, Hellas, Heraklion, Crete, Greece
    date: '2022-11-28'
    refcontent: 2022 IEEE Conference on Standards for Communications and Networking (CSCN)
  RFC7252: coap
  RFC8824: coap-schc
  I-D.ietf-schc-8824-update: coap-schc-bis

...

--- abstract

This document specifies a compression scheme for Datagram Transport Layer Security (DTLS) datagrams in Static Header Compression (SCHC).


--- middle

# Introduction

Datagram Transport Layer Security (DTLS) is an essential part of many IoT deployments.
The most common use case is a deployment of the Constrained Application Protocol (CoAP) over DTLS
(CoAPS) {{-coap}}.
Inspired by earlier work {{SCHC-DTLS-paper}}, this document provides a compression scheme for Static
Header Compession (SCHC) {{-schc}}.
While a compression scheme for CoAP already exists (see {{-coap-schc}}, {{-coap-schc-bis}}), the CoAP
header is encrypted in CoAPS and thus transparent to the SCHC C/D.
As such, a compression scheme for DTLS is needed.
Inner CoAP header could also be compressed, however, this is out of scope of this document.

TBD polish introduction once the direction is a bit clearer.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

TBD

# DTLS compression

This section defines DTLS fields that can be compressed with SCHC.
{{SCHC-DTLS-paper}} describes a rigid compression scheme that only allows for one configuration of
DTLS 1.2 {{-dtls12}} header compression. This specification tries aims for a broader compression that also
allows for compression of DTLS 1.3 headers which differ from DTLS 1.2 headers {{-dtls13}}. Older
versions of DTLS are not supported.

## Record Layer

{{-dtls12}}, {{-dtls13}}, and {{-tls13}} defines the format of DTLS packets in the presentation
language defined in {{-tls13}}, starting from the DTLS record layer. Multiple DTLS records may be
put into a single datagrame. However, for the purposes of SCHC it is RECOMMENDED, to have one record
per datagram. Otherwise, fitting one packet type into a single group might not become difficult.
Some fields mentioned in {{-dtls13}} for the record layer are either described in {{-dtls12}} or
{{-tls13}}. The fields of the DTLS record layer are as follows, we cite the appropriate document of
its definition below.

* `type`: An 8-bit field that encodes the higher-level protocol used to process the enclosed
  fragment. Its values are assigned by IANA in the TLS ContentType registry as per {{-tls13}}.
* `version`: A 16-bit field that defines the version of the DTLS protocol. It MUST be set to {254,
  253} for both DTLS 1.2 {{-dtls12}} and DTLS 1.3 (where it is included for legacy purposes) except
  for the initial ClientHello with DTLS 1.3 where it also may be {254, 255} for compatibility
      purposes {{-dtls13}}.
* `epoch`: A 16-bit field that is a counter of cipher state changes in DTLS 1.2 {{-dtls12}} and the
  least significant 2 bytes of the connection epoch value in DTLS 1.3 {{-dtls13}}.
* `sequence_number`: A 48-bit filed that is the sequence number for the given record {{-dtls12}}.
* `length`: A 16-bit field that is the length (in bytes) of the following fragment. The
  length MUST NOT exceed 2¹⁴ bytes {{-tls13}}.
* `fragment`: The data of length `length` being transmitted. This value is transparent and is
  treated as an independent block to be dealt with by the higher-level protocol specified by the
  `type` field. If compression applies, it is specified in the following {{-tls13}}.

All fields but `fragment` can be covered by SCHC compression rules with the appropriate field
lengths. Since the compression of the `fragment` field is dependent on `type`, however, it is not
possible to create one rule for all DTLS records. The fields of `fragment` are specified in the
following sections If another record follows `fragment` it should be made clear using _var_ length
of a payload field within `fragment`.

## Handshake Messages

Records with `type` = `handshake(22)` are structured by the following fields:

TBD

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
