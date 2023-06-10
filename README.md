# Archive Extension Specification

- **Title:** Archive
- **Identifier:** <https://stac-extensions.github.io/archive/v1.0.0/schema.json>
- **Field Name Prefix:** archive
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr, @constantinius

This extension deals with cases when files are not directly accessible on their
respective storage, but are part of an archive file, such as ZIP or TAR archives.
This extension deals with the details of referencing the archive file and the file inside,
relative to the archives root.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## *Asset / Link Object* fields

The fields in the table below can be used in these parts of STAC documents:
- [ ] Catalogs
- [ ] Collections
- [ ] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [x] Links

| Field Name           | Type                      | Description |
| -------------------- | ------------------------- | ----------- |
| archive:href         | string                    | The location of the file within the archive specified by the `href` field |
| archive:format       | string                    | The mimetype of the archive format |
| archive:type         | string                    | The mimetype of the file within the archive specified by the `href` field |
| archive:start        | integer                   | The offset of the first byte of the file within the archive |
| archive:end          | integer                   | The offset of the last byte of the file within the archive |

### Additional Field Information

#### archive:href

This is a relative or absolute path to locate the assets or linked file within the archive.

#### archive:format

This defines the canonical format of the archive. This can be different as the
asset/links `type`, as this may only refer to the outermost compression used.
For example, TAR files are uncompressed by themselves, so they are usually
wrapped in a compressed file format like gzip (`.gz`) resulting in a filename
`myarchive.tar.gz`. In that example the `type` would be `application/gz` whereas
the `archive:format` would be `application/x-tar`.

#### archive:type

This describes the mimetype of the file within the archive. The same rules and
semantics as with the `type` property apply here as well.

#### archive:start and archive:end

These properties would describe the byte-location of the file within the
archive. This would only be useful if the archive is uncompressed and the files
are stored in a single continuous block. A reader could make use of that
information to directly read this portion of the archive to have direct access
to the file.

## Roles

The following roles should be used as applicable `roles` types in the
[Asset Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#asset-object).

| Role                | Description |
| ------------------- | ----------- |
| archive             | This role is to be used when referencing files that are used as an archive. |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid.
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
