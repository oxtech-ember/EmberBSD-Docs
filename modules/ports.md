# Ports and shared dependencies

[Modules](README.md) · [SDK](sdk.md) · [Package configuration](../configuration/packages-and-sdk.md)

Ports owns third-party recipes, versions, source hashes, patches, and dependency relationships.
It supplies a maintained delta over pkgsrc.
The common graph resolves the dependencies of selected package roots.
The exported source catalog describes available recipes; it does not select all packages for compilation.

## Main units

| Unit | Meaning |
| --- | --- |
| Recipe | How to obtain, patch, build, and package one software component |
| `PKGPATH` | Versionless package identity, such as `net/fetch` |
| `PKGNAME` | Concrete package name with version and package revision |
| Package set | A list of selected roots; dependencies remain in recipes |
| Native role | A package that runs on the development host |
| Target role | A package built for the selected target ABI |

Package selection distinguishes tools that execute on the host from software built for the target.
Read the [selection contract](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/docs/package-selection.md) for exact role syntax.
The same dependency can need separate host and target artifacts.

The provider recipe fixes what goes into a release. Each consumer specifies which
versions it can use. For example, an application linked to a shared library may
accept a fixed implementation under the same ABI. An executable containing a
static copy needs a new link to receive the fix. A change to an inline function
in a header can require recompiling the files that included it. A package using
a private interface can require an exact provider revision.

The graph retains the evaluated requirement on each consumer's dependency edge.
This lets two consumers of the same library express different constraints. Build
planning also needs to know which headers, tools and libraries contributed to an
artifact before it can skip work safely. [Ports update planning](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/docs/update-ports.md)
Common recipes and patches remain shared.

## Relationship to SDK and OS

The SDK frontend requests a selection and delegates source planning/building to Ports.
The OS retains its native build integration, headers, and base ABI.
An application consumes the common dependencies instead of maintaining a private recipe copy.

The SDK client prefers a compatible installed package, then a signed binary, then an available source-build path.
Signature or integrity failure is an error; it does not request a source fallback.
Explicit source builds install into configured build prefixes and sysroots.
That operation does not deploy software to a board.

## Read the owner

- [Ports overview](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/README.md)
- [Package selection contract](https://github.com/oxtech-ember/EmberBSD-Ports/blob/main/docs/package-selection.md)
- [Package sets](https://github.com/oxtech-ember/EmberBSD-Ports/tree/main/profiles/package-sets)
- [SDK binary client](https://github.com/oxtech-ember/EmberBSD-SDK/blob/main/docs/sdk-client.md)

Experimental probes, packaged builds, and installed runtime checks have separate status.
Use the recipe or profile's stated target and validation limits.
