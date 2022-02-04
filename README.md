---
arc: 333
title: Community Algorand Standard Asset Parameters Conventions for NFTs Controlling DAO Governance
status: Living Standard
---

# Community Algorand Standard Asset Parameters Conventions for NFTs Controlling DAO Governance

## Summary

We introduce community conventions for the parameters of Algorand Standard Assets (ASAs) containing information guiding DAO governance.

## Abstract

The goal of these conventions is to store the relevant DAO information of a given ASA on-chain. This ARC differs from [ARC69](https://github.com/algokittens/arc69/edit/master/README.md) by adding necessary basic DAO and unique communication platform information as well as current member, role, team, and project statuses using of on-chain metadata. Furthermore, since asset configuration transactions are used to store the metadata, this ARC can be applied to existing ASAs.

While mutability helps with backwards compatibility and other use cases, some use cases call for immutability. In these cases, the ASA manager MAY remove the manager address, after which point the Algorand network won't allow anyone to send asset configuration transactions for the ASA. This effectively makes the latest valid ARC-333 metadata immutable.
 

## Specification

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

An ARC-333 ASA has an associated JSON Metadata file, formatted as specified below, that is stored on-chain in the most recent asset configuration transaction.

### ASA Parameters Conventions

The ASA parameters should follow the following conventions:

* *Unit Name* (`un`): no restriction. 
* *Asset Name* (`an`): no restriction.
* *Asset URL* (`au`): a URI pointing to digital media file. This URI:
    * **SHOULD** be persistent.
    * **SHOULD** link to a file small enough to fetch quickly in a gallery view.
    * **MUST** follow [RFC-3986](https://www.ietf.org/rfc/rfc3986.txt) and **MUST NOT** contain any whitespace character.
    * **SHOULD** specify media type with `#` fragment identifier at end of URL. This format **MUST** follow: `#i` for images, `#v` for videos, `#a` for audio, `#p` for PDF, or `#h` for HTML/interactive digital media.  If unspecified, assume Image.
    * **SHOULD** use one of the following URI schemes (for compatibility and security): *https* and *ipfs*:
        * When the file is stored on IPFS, the `ipfs://...` URI **SHOULD** be used. IPFS Gateway URI (such as `https://ipfs.io/ipfs/...`) **SHOULD NOT** be used.
    * **SHOULD NOT** use the following URI scheme: *http* (due to security concerns).
* *Asset Metadata Hash* (`am`): the SHA-256 digest of the full resolution media file as a 32-byte string (as defined in [NIST FIPS 180-4](https://doi.org/10.6028/NIST.FIPS.180-4))
    * **OPTIONAL**
* *Freeze Address* (`f`): 
    * **SHOULD** be empty, unless needed for royalties or other use cases
* *Clawback Address* (`c`): 
    * **SHOULD** be empty, unless needed for royalties or other use cases


There are no requirements regarding the manager account of the ASA, or the reserve account. However, if immutability is required the manager address **MUST** be removed.

### JSON Metadata File Schema

```json
{
    "title": "Token Metadata",
    "type": "object",
    "properties": {
        "standard": {
            "type": "string",
            "value": "arc333",
            "description": "(Required) Describes the standard used."
        },
        "description": {
            "type": "string",
            "description": "Describes the asset to which this token represents."
        },
        "external_url": {
            "type": "string",
            "description": "A URI pointing to an external website. Borrowed from Open Sea's metadata format (https://docs.opensea.io/docs/metadata-standards)."
        },
        "media_url": {
            "type": "string",
            "description": "A URI pointing to a high resolution version of the asset's media."
        },
        "media_integrity": {
            "type": "string",
            "description": "The SHA-256 digest of the file pointed by the URI media_url. The field value is a single SHA-256 integrity metadata as defined in the W3C subresource integrity specification (https://w3c.github.io/webappsec-subresource-integrity)."
        },
        "media_mime_type": {
            "type": "string",
            "description": "The MIME type of the file pointed by the URI media_url."
        },
        "properties": {
            "type": "object", 
            "description": "Properties following the EIP-1155 'simple properties' format. (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1155.md#erc-1155-metadata-uri-json-schema)"
        },
        "mime_type": {
            "type": "string",
            "description": "Describes the MIME type of the ASA's URL (`au` field)."
        },
        "integrity": {
            "type": "string",
            "description": "The SHA-256 digest of the file pointed by the ASA's URL (`au` field). The field value is a single SHA-256 integrity metadata as defined in the W3C subresource integrity specification (https://w3c.github.io/webappsec-subresource-integrity)."
        },
        "attributes": {
            "type": "array",
            "description": "(Deprecated. New NFTs should define attributes with the simple `properties` object. Marketplaces should support both the `properties` object and the `attributes` array). The `attributes` array follows Open Sea's format: https://docs.opensea.io/docs/metadata-standards#attributes"
        },
        "dao": {
            "type": "object",
            "description": "Properties following the ARC-33 'DAO information' format. (https://github.com/Dad-DAO/ARC33"
        }
    }
}
```
The `standard` field is **REQUIRED** and **MUST** equal "arc333". All other fields are **OPTIONAL**. If provided, the other fields **MUST** match the description in the JSON schema.

The URI field (`external_url`) is defined similarly to the Asset URL parameter `au`.
However, contrary to the Asset URL, the `external_url` does not need to link to the digital media file.



#### MIME Type

In addition to specifying a data type in the ASA's URL (`au` field) with a URI fragment (ex: `#v` for video), the JSON Metadata schema also allows indication of the URL's MIME type (ex: `video/mp4`) via the `mime_type` field.



#### Examples

##### Basic Example

An example of an ARC-333 JSON Metadata file for a song follows. The properties array proposes some **SUGGESTED** formatting for token-specific display properties and metadata.

```json
{
    "standard": "arc333",
    "name": "Dad DAO NFT#1",
    "description": "A Learning Community and DApp Incubator",
    "external_url": "https://www.daddao.org",
    "media_url": "ipfs://QmUnCYRWY8w9i37Rh2fvAySf6R6YT1BjSa6BAiXP7xS4YA",
    "media_integrity": "QmSMMk23vaxWxPqWRdzXxvY4YWMnTVJRWgowDzd9S8jC6m",
    "mime_type": "image/png",
    "dao": {
        "dao_property": {
            "name": "DadDAO",
            "dao_id": "910249072986828870",
        },
        "member_property": {
            "name": "Broke",
            "member_id": "132999600813506560",
            "role": "Generalist",
            "team": "TBP",
            "project": "Longevity",
            "member_status": "Active",
        },
        "collection_hash": {
            "hash": "a3074c1e17ca34419b79d9926777dcc4ff142460af7f9071d8828198bd2b3eb6"
        }
    }
}      
```

An example of possible ASA parameters would be:

* *Asset Name*: `ARC333 theme song` for example.
* *Unit Name*: `DADDAONFT` for example.
* *Asset URL*: `ipfs://QmUnCYRWY8w9i37Rh2fvAySf6R6YT1BjSa6BAiXP7xS4YA#i`
* *Metadata Hash*: the 32 bytes of the SHA-256 digest of the high resolution media file.
* *Total Number of Units*: 1
* *Number of Digits after the Decimal Point*: 0

#### Mutability

##### Rendering

Clients SHOULD render an ASA's latest ARC333 metadata. Clients MAY render an ASA's previous ARC69 metadata for changelogs or other historical features.

##### Updating ARC333 metadata

Managers MAY update an ASA's ARC333 metadata. To do so, they MUST send a new `acfg` transaction with the entire metadata represented as JSON in the transaction's `note` field.

##### Making ARC333 metadata immutable

Managers MAY make an ASA's ARC333 immutable. To do so, they MUST remove the ASA's manager address with an `acfg` transaction.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
