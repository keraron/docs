# Agent Directory Architecture

ADS is a distributed directory service designed to store metadata for AI agent
applications. This metadata, in the form of directory records, can be used to
discover agent applications that implement specific skills to solve various
problems.

The current implementation supports distributed directories that can
interconnect via a content-routing protocol. This protocol maps announced skills
to directory record identifiers and provides a list of directory servers that
are currently storing those records.

A directory record must include announced skills that belong to a skill
taxonomy, as defined in the [Taxonomy of AI Agent Skills](taxonomy.md) from
[OASF](oasf.md).

All data in a record is modeled using [OASF](oasf.md), but only skills are used
to implement content routing in the distributed network of directory servers.

The ADS specification is a work in progress and is published as an Internet
Draft in the [ADS Spec](https://spec.dir.agntcy.org). The sources are available
in the [ADS Spec sources](https://github.com/agntcy).

The current reference implementation is written in Go and implements server and
client nodes with interfaces based on gRPC and protocol buffers. The directory record
store is based on [ORAS](https://oras.land) (OCI registry as storage), and
data distribution is implemented using the [zot](https://zotregistry.dev) OCI
server implementation.

Content-routing is implemented using a Kedemlia DHT using the go implementation
of the [libp2p](https://libp2p.io) project.

## Data models registration

## Data models discovery
