---
title: "Bonjour, CUPS: The dnssd Backend Explained"
date: 2016-07-05 21:17:00.000000000 -07:00
tags:
  - printing
  - network-printing-is-hell
  - cups
  - bonjour
  - dnssd
  - mdns
---

Probably the most pervasive system for printer discovery -- at least in
the SOHO market -- is Apple's [Bonjour]. It's based on [mDNS], which
provides decentralized name announcement and resolution. [DNS-SD] is
layered on top of mDNS to provide a mechanism for service discovery. By
their powers combined, clients can find services provided by other nodes
on their local network (i.e. networked printers) without needing a
central directory or any manual configuration. Apple has [published a
standard][airprint] for how network printers should advertise themselves.

[Bonjour]: https://developer.apple.com/bonjour/
[mDNS]: https://tools.ietf.org/html/rfc6762
[DNS-SD]: https://tools.ietf.org/html/rfc6763
[airprint]: https://developer.apple.com/bonjour/printing-specification/bonjourprinting-1.2.pdf


CUPS has a special backend for working with Bonjour printers called
`dnssd`. It provides two distinct functions: browsing for new printers
and addressing of existing printers. Unfortunately it doesn't seem to
have any real documentation. Ususally it Just Works&trade;, but I recently
hit a case where it didn't and had to go read the source code. I've
documented what I found for future reference.


## Discovery

When invoked in discovery mode (as `dnssd` with no arguments), the
`dnssd` backend requests that the local mDNS responder browse for
service records with any of these types:

* `_fax-ipp._tcp`
* `_ipp._tcp`
* `_ipp-tls._tcp`
* `_ipps._tcp`
* `_pdl-datastream._tcp`
* `_printer._tcp`
* `_riousbprint._tcp` (only with mDNSresponder, not Avahi)

The backend then resolves each of the discovered services.  If an IPP or
IPPS service record includes the non-standard property `printer-type`
it's assumed to be shared by a CUPS server. If an LPD service includes
that key it's assumed to be an LPR gateway for a CUPS server and
ignored.  The backend then chooses the best service for each device
following the priority scheme laid out in the standard and reports it
using the standard [`backend(7)`][man-backend] discovery format, with
the fields set as described below.

[man-backend]: https://www.cups.org/documentation.php/doc-1.7/man-backend.html#DEVICE_DISCOVERY


`device-class`
:   Always set to `network`

`device-uri`
:   A URL is built using `dnssd` as the scheme and the fully-qualified
    service name as the host. If the service is assumed to be hosted by
    CUPS the path is set to `/cups`, otherwise `/` is used. The URI will
    end up looking like this:

        dnssd://HP%20LaserJet%204050%20Series._pdl-datastream._tcp.local/

`device-make-and-model`
:   The model is taken from `usb_MDL`, `product` (with the parentheses
    removed), or `ty`, whichever comes last in the service record. The
    make is taken from the value of the `usb_MFG` key or, if none of the
    `usb_` keys are present and a model key is, from the first word of
    the model. The make and model are then concatenated separated by a
    single space. This causes the first workd of the model be repeated
    if `usb_MFG` wasn't specified.

`device-info`
:   Set to the unqualified service name.

`device-id`
:   Any properties starting with `usb_` (i.e. `usb_MFG`, `usb_MDL`, and
    `usb_CMD`) will be used to set the matching parts of the IEEE 1284
    device ID string. If no `usb_` properties were found, but a model
    name was seen from a different property, the `MFG` and `MDL` 1284
    properties are set from the make and model strings determined under
    `device-make-and-model`, above. If the `CMD` 1284 property is not
    set it's generated from the `pdl` service property.

    If both the `MDL` and `CMD` values are generated as described above,
    a bug causes the semicolon separating them to be omitted. This will
    cause a device ID that looks like this:

        MFG:HP;MDL:LaserJet 4050 Series CMD:PS,PCL;

    Such a device ID string prevents automatic driver detection as the
    CMD property is read as part of the model. This bug was fixed in
    CUPS v2.2b1 and the fix will presumably be included in v2.2.

`device-location`
:   Never set, even though the standard provides the `note` field for
    this purpose.



## Addressing

If the `dnssd` backend is invoked as a normal backend it attempts to
resolve the device URI, then turns around and calls the backend
indicated by the service type. This behavior is somewhat redundant as
all the built-in backends will resolve mDNS service names on their own
and the `dnssd` backend doesn't support non-built-in backends. Perhaps
it's done that way in case the service properties change without
changing the fully qualified service name.

The hostname will be resolved as a service name if it contains the
string `._tcp`. Resolution will first be attempted in the `local`
domain, then in the domain specified in the URL. If the device fails to
respond it's marked offline. Otherwise, a new URI is generated based on
the DNS-SD service record. The scheme is set according to the following
table:

| Service Type      | Scheme /Backend |
|-------------------|-----------------|
| `_ipps`           | `ipps`          |
| `_ipp-tls`        | `ipps`          |
| `_ipp`            | `ipp`           |
| `_fax-ipp`        | `ipp`           |
| `_http`           | `http`          |
| `_https`          | `https`         |
| `_printer`        | `lpd`           |
| `_pdl_datastream` | `socket`        |
| *anything else*   | `riousbprint`   |


The hostname is set to the one advertised in the service record, usually
an mDNS hostname. If the service advertises a hostname in the `local`
domain but the original URI used a different domain, the resolver will
attempt to find a non-local name for the host by looking up its IP
address and then looking up the reverse DNS entry of that IP.  If the
reverse hostname is not in the `local` domain it will be used instead of
the one advertised by the service.

The port is taken from the service record. The path is set to the value
of the `rp` service property. For `_ipp-fax` services `rpo` is used
instead. If the service is one of the IPP variants the query is set to
`?snmp=false`.

The `dnssd` backend then invokes the backend of the same name as the
resolved URI scheme with the resolved URI. It passes all other arguments
and the standard file descriptors on intact.
