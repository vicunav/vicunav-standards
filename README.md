# Vicunav Standards

Canonical technical standards shared by every repository in the Vicunav ecosystem.
Consumer repositories pin this project as the `docs/standards/` Git submodule, so each
change is versioned and adopted deliberately.

This repository owns cross-cutting rules only. Architecture and roadmap decisions live
in [`vicunav-hub`](https://github.com/vicunav/vicunav-hub), while implementation details
remain in the package that owns them.

## Contents

- [Compatibility](docs/compatibility.md)
- [Security](docs/security.md)
- [Naming conventions](docs/naming.md)
- [Git workflow](docs/git.md)
- [Accessibility](docs/accessibility.md)
- [Testing](docs/testing.md)
- [Documentation language](docs/documentation-language.md)
- [CC BY 4.0 License](LICENSE)

## Using the standards

Initialize the submodule after cloning a consumer repository:

```bash
git submodule update --init --recursive
```

To adopt a newer standards release, update the submodule in a dedicated issue and pull
request, review the changed rules, and validate the consumer before merging. Do not copy
individual standards into another folder or maintain local variants.

## Status

The seven standards listed above are active. New rules belong here only when they apply
across the ecosystem; repository-specific guidance belongs in that repository's
`AGENTS.md` or `docs/` directory.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the change workflow.
