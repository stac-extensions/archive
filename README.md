# Archive Extension Specification

- **Title:** Archive
- **Identifier:** <https://stac-extensions.github.io/archive/v1.0.0/schema.json>
- **Field Name Prefix:** -
- **Scope:** Item, Collection, Catalog
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This extension deals with cases when files are not directly accessible on their
respective storage, but are part of an archive file, such as ZIP or TAR archives.
This extension deals with the details of referencing the archive file and the file inside,
relative to the archives root.

- Examples:
  - [STAC Item](examples/item.json)
  - [STAC Collection](examples/collection.json)
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [ ] Collections
- [ ] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [x] Links

| Field Name | Type              | Description |
| ---------- | ----------------- | ----------- |
| archive    | \[Archive Object] | A list of 1+ (relevant) files within the archive. |

Note: The `archive` array can be empty in `item_assets`.

### Archive Object

This object works like a normal Asset or Link, all properties that can be used in an
Asset Object or Link Object can also be provided in the Archive Object, including `archive` itself.

The following fields from the STAC specification are commonly provided:

| Field Name  | Type              | Description |
| ----------- | ----------------- | ----------- |
| href        | string            | **REQUIRED.** The relative (to the archive root) or absolute path of the file within the archive. |
| type        | string            | The media type of the file within the archive. |
| roles       | \[string]         | The roles of the file within the archive. |
| range       | \[integer]        | The byte location of the file within the archive, e.g. for HTTP range requests. |
| title       | string            | The title of the file within the archive. |
| description | string            | The description of the file within the archive. |
| bands       | \[Band Object]    | If applicable, the bands of the file within the archive. |
| archive     | \[Archive Object] | If applicable, for double nesting of archives (see below). |

Note: The `href` is OPTIONAL in `item_assets`.

The following example fields from other extensions could be useful:

| Field Name    | Type    | Description                                                | Extension |
| ------------- | ------- | ---------------------------------------------------------- | --------- | 
| file:size     | integer | The uncompressed file size of the file within the archive. | [File](https://github.com/stac-extensions/file) |
| file:checksum | string  | The checksum of the uncompressed file in the archive.      | [File](https://github.com/stac-extensions/file) |
| ...           | ...     | ...                                                        | ... |

#### range

This property describes the byte location of the file within the archive.
This would only be useful if the files are stored in a single continuous block.
A reader could make use of that information to directly read this portion of
the archive to have direct access to the file, e.g. on the file system or
through HTTP Range requests.
It is a two-element array of zero-based offset of the first and last byte (inclusive)
of the file within the archive.
The values are meant to be provided in a way that they can be used in HTTP
Range requests without a change.

Note: All other fields are defined in the STAC specification or in STAC extensions,
while range is a new field defined only in this extension at this point.

## Double nested archive formats

"Double" archives such as `tar.gz` (i.e. a TAR file within a GZIP file)
must be provided as `archive` within an Archive Object.

Example:

```json
{
  "assets": {
    "example": {
      "href": "example.tar.gz",
      "type": "application/gz",
      "roles": ["data"],
      "archive": [
        {
          "href": "example.tar",
          "type": "application/x-tar",
          "roles": ["data"],
          "archive": [
            {
              "href": "example.csv",
              "type": "text/csv",
              "roles": ["data"],
            }
          ]
        }
      ]
    }
  }
}
```

## Roles

The following roles should be used as applicable `roles` types in the
[Asset Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#asset-object).

| Role    | Description |
| ------- | ----------- |
| archive | This role is used when an entity provides the files both archived and extracted. In this case, the additional archive file provides this role. This is to avoid downloading files both as archive and extracted. |

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
