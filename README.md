# BagIt-LDP

## Table of Contents

1. [Introduction](#introduction)
2. [Media Type and LDP Considerations](#media-type-and-ldp-considerations)
3. [Bagit Profile](#bagit-profile)
4. [Context And Typed Tags](#context-and-typed-tags)
5. [Document Continuation](#document-continuation)
6. [References](#references)

## Introduction

The data structure resulting from a parsed BagIt tag file is a map of string labels to untyped string values in order. As such, the tag file is losslessly translatable to JSON-LD. This primary goal of this document is to outline a BagIt-Profile and parsing practice to leverage this JSON-LD compatibility to serialize and deserialize LDP resources as BagIt archives. The secondary goal is to produce object serializations that are sufficiently backwards-compatible to be used as plain BagIt archives in non-LDP contexts, e.g. [AP Trust](http://academicpreservationtrust.org/).

### Rationale

1. The tagfile/json-ld translatability
2. The fact that `<iana:describedby>` is a predicate that indicates graph continuation
3. The fact that json-ld's `@value` attribute can point to a relative fileUri for binary content (so that a node could have a separate `@id` uri and `@value` uri).

If you have some ttl of your LDP object -- with relative URIs -- and you have a `Described-By` tag in your bag-info that points to the ttl, and you have a context tagfile that says:

> { "Described-By" : { "@id" : "iana:describedby", "@type" : "xsd:anyURI" } }

Then you basically have a graph serialization in the payload as well as including a context.

## Media Type and LDP Considerations

Spec needs a media type that indicates a zipped BagIt archive: `application/zip+bagit` ?

* LDP clients that `POST` BagIt serializations **MUST** indicate the ldp:RDFSource or ldp:Container interaction model.
* LDP clients that require BagIt serializations on `GET` **MUST** indicate the requirement in Accept.
* LDP servers that support BagIt serializations on `POST` **MUST** advertise support in Accept-Post.
* LDP servers that support BagIt serializations on `PUT` **MUST** advertise support in Accept-Put.
* LDP servers that do not support BagIt serializations **SHOULD** respond with `HTTP 415` to `POST` with a BagIt serialization.

## Bagit Profile

## Context And Typed Tags

Since tag names are case insensitive, the context information below should be considered to match all case variation of the tag names.

JSON-LD disallows aliasing `@context`; the "reserved context" alias below is effective rather than literal.

### Default Context
#### Reserved Context

```json
{
  "BagIt-Context" : "@context",
  "Bagging-Date" : {"@type":"xsd:date"},
  "BagIt-Profile-Identifier" : {"@type:"xsd:anyURI"},
  "BagIt-Payload-URI" : {"@type:"xsd:anyURI"},
  "bagit-payload" : "BagIt-Payload-URI"
}
```

#### Default Context

```json
{
  "External-Identifier" : "@id",
  "Described-By" : {
    "@id" : "http://www.iana.org/assignments/link-relations/describedby",
    "@type" : "xsd:anyURI",
  }
}
```

### Examples

#### bag-info.txt
#### context.txt

## Contained resource URIs

* Resolution of relative file URIs to bag root
* Resolution of relative URIs to context, starting at External-Identifier
* bagit-payload URI and json-ld expansion
* Binary content indicated with `owl:sameAs <fileUri>`? `"@value" : <fileUri>`?
* Content serialized for proxied resources?

## Document Continuation

A tag with `@id` of `<http://www.iana.org/assignments/link-relations/describedby>` **SHOULD** be a relative URI pointing to additional description of the resource currently in context. If this is serialized data (a file URI), the tag's context **SHOULD** indicate its MIME with `@format`.

## Cached Resources and Binary Content in the Bag

Use of `@value = file` uri for cached published context, nonRdfSource content with URI id.

## References

* [BagIt 0.97](https://tools.ietf.org/html/draft-kunze-bagit-13)
* [BagIt Profiles](https://github.com/ruebot/bagit-profiles)
* [JSON-LD](http://json-ld.org/spec/latest/)
* [LDP](https://www.w3.org/TR/ldp/)
