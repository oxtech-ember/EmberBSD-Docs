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

A root with `@native` selects the host role.
A root without that suffix selects the target role in the package selector.
The same dependency can need separate host and target artifacts.
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
