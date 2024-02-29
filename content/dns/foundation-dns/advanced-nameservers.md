---
pcx_content_type: concept
title: Advanced nameservers
weight: 2
---

# Advanced nameservers

TBD >> Might make sense to change title and structure

## Anycast network groups

To increase resiliency, advanced nameserver IPs are advertised by only one of two {{<glossary-tooltip term_id="anycast">}}anycast{{</glossary-tooltip>}} network groups.

The two groups consist of data centers that are geographically equally distributed.

{{<details header="United Kingdom example">}}

{{<table-wrap>}}
| Advanced nameservers     | IPs      | Group   | Data centers |
|--------------------------|--------- |---------|------------------------------------|
| blue.foundationdns.com   | `<IPv4>`<br />`<IPv6>` | A | London and Edinburgh |
| blue.foundationdns.net   | `<IPv4>`<br />`<IPv6>` | B | Manchester           |
| blue.foundationdns.org   | `<IPv4>`<br />`<IPv6>` | A | London and Edinburgh |
{{</table-wrap>}}

{{</details>}}

In DNS resolution, a resolver eventually acquires a list of all IPs where authoritative nameservers for a domain can be reached, and will then usually prefer the IP with the best resolution performance.

When, instead of advertising all IPs in all data centers, this group logic is applied, resiliency is improved because, if one of the data centers experiences a localized issue, the resolver can fall back to an IP advertised by the next closest data center.

## Dedicated release process

Zones using advanced nameservers are also less exposed to incidents or software regression.

The dedicated release process means that only stable changes, that have been in production for a while, will reach advanced nameservers.

## Nameservers hosting and assignment

While standard Cloudflare nameservers are hosted under `ns.cloudflare.com` or `secondary.cloudflare.com`, advanced nameservers use different domains:

- `foundationdns.com`
- `foundationdns.net`
- `foundationdns.org`

Using the different TLDs (`.com`, `.net`, and `.org`) and making these available only to enterprise accounts allows for better predictability and consistency in nameserver assignment.

There should also be less conflicts when guaranteeing that directly descending zones do not have the same nameserver set.

{{<details header="Descending zones example">}}

Consider the domain `example.com`, and subdomains `abc.example.com` and `123.example.com`:

- `abc.example.com` and `123.example.com` directly descend from `example.com` and cannot have the same nameservers as `example.com`.
- `abc.example.com` and `123.example.com` are sibling domains and can have the same nameservers.
- `new.abc.example.com` directly descends from both `abc.example.com` and `example.com`, and cannot have the same nameservers as them, but can have the same nameservers as `123.example.com`.

{{</details>}}