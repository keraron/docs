# Agent Directory Architecture

ADS is a distributed directory service designed to store metadata for AI agent
applications. This metadata, in the form of directory records, can be used to
discover agent applications that implement specific skills to solve various
problems.

The current implementation supports distributed directories that can
interconnect via a content-routing protocol. This protocol maps announced skills
to directory record identifiers and provides a candidate list of directory
servers that are currently storing those records.

A directory record must carry announced skills that belong to a skill taxonomy
[Taxonomy of AI Agent Skills](taxonomy.md) that is taken from [OASF](oasf.md).

All data in a record is modelled using [OASF](oasf.md) but only skills are used
to implement content routing in the distributed network of directory servers.

## Data models registration

## Data models discovery
