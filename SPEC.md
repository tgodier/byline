# Byline Specification

**Version 0.1.0 (Draft)**

Canonical URL: https://bylinespec.org/spec

---

## Abstract

Byline is an extension vocabulary for RSS, Atom, and JSON Feed that provides structured author identity, context, and content perspective. It solves *content collapse*: the loss of context that occurs when content from diverse sources arrives in a unified stream.

---

## The Problem

RSS, Atom, and JSON Feed excel at syndicating content but fail at syndicating *context*.

When content from a personal blog, a corporate press release, a journalist's reporting, and a satirical newsletter arrive in the same feed reader, they are structurally identical. Readers must rely on memory and domain recognition to answer basic questions:

- Who is this person?
- What is their relationship to this topic?
- Is this news, opinion, or satire?
- Should I read this differently than the previous item?

Existing author elements provide minimal help:

| Format | Element | Limitation |
|--------|---------|------------|
| RSS 2.0 | `<author>` | Email address only |
| RSS 2.0 | `<dc:creator>` | Unstructured text |
| Atom | `<author>` | Name, URI, email (no context) |
| JSON Feed | `authors` | Name, URL, avatar (no context) |

Byline fills this gap by providing a vocabulary for:

- **Identity**: Who is this person, with links to verify
- **Context**: A brief description of who they are and why it matters
- **Perspective**: What type of content is this
- **Affiliation**: Relevant relationships and disclosures
- **Presence**: Links to /now and /uses pages
- **Personalization**: Author-specified theming

---

## Design Goals

1. **Minimal addition.** Extend, don't replace. Work alongside existing elements.
2. **Progressive enhancement.** Feeds work without Byline. Readers that don't support it degrade gracefully.
3. **Five-minute implementation.** A publisher can add meaningful Byline data quickly.
4. **Decentralized identity.** No registries. Identity through URIs and linked profiles.
5. **Human readable.** Elements should make sense when read directly.
6. **Format agnostic.** Equal support for RSS, Atom, and JSON Feed.

---

## Namespace Declaration

### Namespace URI

```
https://bylinespec.org/1.0
```

### RSS 2.0

```xml
<rss version="2.0" xmlns:byline="https://bylinespec.org/1.0">
```

### Atom

```xml
<feed xmlns="http://www.w3.org/2005/Atom"
      xmlns:byline="https://bylinespec.org/1.0">
```

### JSON Feed

Use the `_byline` extension key at feed and item level.

---

## Core Elements

### Person

Defines a person or entity that creates content.

**RSS/Atom:**

```xml
<byline:person id="annie">
  <byline:name>Annie Park</byline:name>
  <byline:context>Designer and photographer. Previously at Figma. Writes about creative tools and process.</byline:context>
  <byline:url>https://annie.example.com</byline:url>
  <byline:avatar>https://annie.example.com/avatar.jpg</byline:avatar>
  <byline:profile href="https://mastodon.social/@annie" rel="mastodon"/>
  <byline:profile href="https://github.com/annie" rel="github"/>
  <byline:now>https://annie.example.com/now</byline:now>
  <byline:uses>https://annie.example.com/uses</byline:uses>
  <byline:theme color="#4A90A4" accent="#FF6B6B" style="light"/>
</byline:person>
```

**JSON Feed:**

```json
{
  "id": "annie",
  "name": "Annie Park",
  "context": "Designer and photographer. Previously at Figma.",
  "url": "https://annie.example.com",
  "avatar": "https://annie.example.com/avatar.jpg",
  "profiles": [
    { "href": "https://mastodon.social/@annie", "rel": "mastodon" },
    { "href": "https://github.com/annie", "rel": "github" }
  ],
  "now": "https://annie.example.com/now",
  "uses": "https://annie.example.com/uses",
  "theme": {
    "color": "#4A90A4",
    "accent": "#FF6B6B",
    "style": "light"
  }
}
```

#### Person Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `id` | Yes | Unique identifier within this feed, used for references |

#### Person Child Elements

| Element | Required | Description |
|---------|----------|-------------|
| `name` | Yes | Display name |
| `context` | No | Human-readable bio or description (max 280 characters recommended) |
| `url` | No | Primary URL (personal site, about page). May appear multiple times. |
| `avatar` | No | URL to profile image |
| `profile` | No | External profile link. May appear multiple times. |
| `now` | No | URL to /now page |
| `uses` | No | URL to /uses page |
| `theme` | No | Display theming preferences |

---

### Organization

Defines an organization associated with the feed or a person.

**RSS/Atom:**

```xml
<byline:org id="ttr">
  <byline:name>The Tech Review</byline:name>
  <byline:url>https://thetechreview.example.com</byline:url>
  <byline:type>news</byline:type>
  <byline:theme color="#1A1A2E" accent="#E94560" style="dark"/>
</byline:org>
```

**JSON Feed:**

```json
{
  "id": "ttr",
  "name": "The Tech Review",
  "url": "https://thetechreview.example.com",
  "type": "news",
  "theme": {
    "color": "#1A1A2E",
    "accent": "#E94560",
    "style": "dark"
  }
}
```

#### Organization Types

| Value | Description |
|-------|-------------|
| `news` | News organization or publication |
| `company` | For-profit company |
| `nonprofit` | Non-profit organization |
| `government` | Government entity |
| `personal` | Personal brand or solo operation |
| `community` | Community-driven project |
| `academic` | University, research institution |

---

### Author Reference

Links an item to its author. Can reference a person defined at feed level, or define one inline.

**Reference by ID:**

```xml
<byline:author ref="annie"/>
```

```json
{ "author": "annie" }
```

**Inline Definition:**

```xml
<byline:author>
  <byline:person id="guest-sara">
    <byline:name>Sara Mitchell</byline:name>
    <byline:context>Guest contributor. Accessibility consultant.</byline:context>
  </byline:person>
</byline:author>
```

---

### Role

The author's relationship to the content or publication.

```xml
<byline:role>staff</byline:role>
```

| Value | Description |
|-------|-------------|
| `creator` | Primary author (default if omitted) |
| `editor` | Edited or curated |
| `guest` | Guest contributor |
| `staff` | Staff writer or employee |
| `founder` | Founded or owns the publication |
| `contributor` | Occasional contributor |
| `bot` | Automated or AI-generated |

---

### Perspective

The nature or intent of the content. This is the key element for addressing content collapse. It tells the reader *how* to read the content.

```xml
<byline:perspective>analysis</byline:perspective>
```

| Value | Description |
|-------|-------------|
| `personal` | Personal opinion, experience, or reflection |
| `reporting` | News reporting, factual coverage |
| `analysis` | Analysis or commentary |
| `official` | Official statement or announcement |
| `sponsored` | Paid or sponsored content |
| `satire` | Satirical or intentionally humorous |
| `review` | Review or critique |
| `announcement` | Product launch, release notes, changelog |
| `tutorial` | Educational, how-to content |
| `curation` | Curated links or recommendations |
| `fiction` | Creative fiction |
| `interview` | Interview or Q&A format |

Readers may use perspective to display visual indicators (badges, icons), filter or group content, and adjust presentation style.

---

### Affiliation

Declares relevant affiliations or conflicts of interest for a specific item.

**RSS/Atom:**

```xml
<byline:affiliation>
  <byline:org-ref ref="apple"/>
  <byline:relationship>employed</byline:relationship>
  <byline:title>Senior Engineer</byline:title>
</byline:affiliation>
```

**JSON Feed:**

```json
{
  "affiliation": {
    "org": "apple",
    "relationship": "employed",
    "title": "Senior Engineer"
  }
}
```

#### Relationship Values

| Value | Description |
|-------|-------------|
| `employed` | Currently employed by |
| `former` | Previously employed by |
| `investor` | Has financial investment in |
| `advisor` | Serves as advisor to |
| `sponsored` | This content is sponsored by |
| `partner` | Business partnership with |
| `board` | Serves on board of |

---

## Identity & Discovery

### Profile Links

Links to external profiles for identity verification and discovery.

```xml
<byline:profile href="https://mastodon.social/@annie" rel="mastodon"/>
```

#### Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `href` | Yes | URL of the profile |
| `rel` | No | Platform or relationship type |

#### Suggested rel Values

| Value | Platform |
|-------|----------|
| `me` | Generic verified link (following rel="me" conventions) |
| `mastodon` | Mastodon / Fediverse |
| `bluesky` | Bluesky |
| `twitter` | Twitter/X |
| `threads` | Threads |
| `linkedin` | LinkedIn |
| `github` | GitHub |
| `instagram` | Instagram |
| `youtube` | YouTube |
| `tiktok` | TikTok |

#### Verification

Profile links are self-asserted claims. Readers implementing verification should:

1. Fetch the linked profile
2. Look for a reciprocal link back to the author's primary URL
3. Follow `rel="me"` mutual linking conventions

---

### Now Page

The `/now` page movement (started by Derek Sivers at nownownow.com) encourages people to publish a page describing what they're focused on *right now*.

```xml
<byline:now>https://annie.example.com/now</byline:now>
```

This provides valuable context: knowing someone just started a new job, is traveling, or is deep in a project changes how you read their posts.

---

### Uses Page

The `/uses` page convention (popularized by Wes Bos at uses.tech) is a page where people list the tools, hardware, and software they use.

```xml
<byline:uses>https://annie.example.com/uses</byline:uses>
```

---

## Theming

Authors can specify display preferences that readers may choose to honor.

**RSS/Atom:**

```xml
<byline:theme color="#4A90A4" accent="#FF6B6B" style="light"/>
```

**JSON Feed:**

```json
{
  "theme": {
    "color": "#4A90A4",
    "accent": "#FF6B6B",
    "style": "light"
  }
}
```

#### Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `color` | No | Primary brand color. 6-digit hex with # prefix. |
| `accent` | No | Secondary/accent color. 6-digit hex with # prefix. |
| `style` | No | Preferred color scheme: light, dark, or auto |

Theming is a **hint**, not a requirement. Readers should ensure sufficient contrast for accessibility (WCAG AA minimum) and allow users to override or disable author theming.

---

## Security & Privacy

### No Tracking

Byline defines no mechanism for tracking readers or reporting analytics to publishers.

### Self-Asserted Claims

All Byline data is self-reported by publishers. Readers should treat:
- `context` as unverified biography
- `profile` links as claims until verified
- `affiliation` as voluntary disclosure

### Privacy Considerations

- No real name requirements. Pseudonymous identities are fully supported.
- No email requirements.
- No PII beyond what authors choose to publish.
- /now and /uses pages are author-controlled.

### Theme Safety

When rendering author-specified colors:
- Always validate hex color format
- Ensure WCAG AA contrast minimums
- Sanitize before use in CSS

---

## FAQ

**Why not just improve the existing author elements?**

RSS and Atom author elements are specified by other standards. Changing them would require broad coordination and break existing implementations. A namespace extension can ship today.

**What if a reader doesn't support Byline?**

Nothing breaks. Byline elements are ignored, standard elements work normally.

**Should I include Byline AND standard author elements?**

Yes. Always include standard elements for maximum compatibility. Byline is additive.

**How do I verify that a profile link is real?**

Follow the `rel="me"` pattern: the profile should link back to the author's primary URL. Mutual linking establishes identity without central authority.

**Can organizations be authors?**

Yes. Create a `person` representing the organization (e.g., "The Acme Team") and use `affiliation` to make the relationship clear.

**What if someone misuses perspective labels?**

Same as any metadata: it's self-reported. Readers can ignore or deprioritize sources that abuse the system. Social trust still matters.

---

## License

This specification is released under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).

You are free to use, adapt, and build upon this work without restriction or attribution.

---

## Changelog

### 0.1.0 (January 2026)

Initial draft specification.

- Core elements: person, org, role, perspective, affiliation
- Identity elements: profile, now, uses
- Theming support
- Format specifications for RSS 2.0, Atom, and JSON Feed
