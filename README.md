# Byline

**Structured identity for syndication feeds.**

Byline is an open specification that extends RSS, Atom, and JSON Feed with author context. It solves *content collapse*: when content from a personal blog, a corporate press release, and a satirical newsletter arrive in your feed reader, they look identical. Byline gives readers the context they need.

## The Spec

The canonical specification lives at **[bylinespec.org/spec](https://bylinespec.org/spec)**.

This repository contains:

- [SPEC.md](./SPEC.md) - The full specification (mirrored from the website)
- [examples/](./examples/) - Sample feeds in RSS, Atom, and JSON Feed formats
- [LICENSE](./LICENSE) - CC0 1.0 Universal (public domain)

## Quick Example

```xml
<rss version="2.0" xmlns:byline="https://bylinespec.org/1.0">
  <channel>
    <title>My Blog</title>

    <byline:contributors>
      <byline:person id="me">
        <byline:name>Your Name</byline:name>
        <byline:context>Writer and developer based in Portland.</byline:context>
      </byline:person>
    </byline:contributors>

    <item>
      <title>My Post</title>
      <byline:author ref="me"/>
      <byline:perspective>personal</byline:perspective>
    </item>
  </channel>
</rss>
```

## What Byline Adds

| Element | Purpose |
|---------|---------|
| `person` | Name, bio, avatar, profile links |
| `perspective` | Content type: personal, reporting, analysis, sponsored, satire, etc. |
| `role` | Author relationship: creator, editor, guest, staff |
| `affiliation` | Relevant disclosures and conflicts of interest |
| `theme` | Author brand colors (optional) |

## Design Principles

1. **Progressive enhancement** - Feeds work without Byline. Readers that don't support it degrade gracefully.
2. **Decentralized identity** - No registries. Identity through URIs and linked profiles.
3. **Format agnostic** - Equal support for RSS 2.0, Atom, and JSON Feed.
4. **Privacy respecting** - No tracking. Pseudonymous identities fully supported.

## Getting Started

- **Publishers**: [Implementation guide](https://bylinespec.org/implement/publishers)
- **Feed readers**: [Parsing guide](https://bylinespec.org/implement/readers)
- **Platforms/CMS**: [Integration guide](https://bylinespec.org/implement/platforms)

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to participate.

Found a bug or have a suggestion? [Open an issue](https://github.com/tgodier/byline/issues).

Want to list your implementation? Open an issue and we'll add it to the website.

## Author

Created by [Terry Godier](https://www.terrygodier.com), an indie iOS developer building Current, an RSS reader designed around the river-of-news philosophy. Byline grew out of the problem Current was trying to solve: when every feed item looks the same, readers lose context.

## License

This specification is released under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/). You are free to use, adapt, and build upon this work without restriction.
