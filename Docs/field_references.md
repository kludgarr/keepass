<!-- Source: https://keepass.info/help/base/fieldrefs.html -->

# Field References

How to put references to data in fields of other entries.

- [Introduction](https://keepass.info/help/base/fieldrefs.html#intro)
- [Placeholder Syntax](https://keepass.info/help/base/fieldrefs.html#syntax)
- [Example](https://keepass.info/help/base/fieldrefs.html#example)

## Introduction

KeePass can insert data stored in different entries into fields of an entry. This means that multiple entries can share a common field (user name, password, ...), and by changing the actual data entry, all other entries will also use the new value.

To create a field reference, you can either use the convenient field references wizard (in the entry editing window, click the 'Tools' button at the bottom left and select 'Insert Field Reference'), or insert the placeholder manually (see the syntax below).

Note that field references are intended for referencing data stored in *different* entries. If you want to insert data from the *same/current* entry, you should use local placeholders, like `{TITLE}` and `{S:FieldName}`; see [Placeholders](https://keepass.info/help/base/placeholders.html).

## Placeholder Syntax

The placeholder syntax for field references is the following:

`{REF:<WantedField>@<SearchIn>:<Text>}`

The *WantedField* and *SearchIn* parts need to be replaced by 1-letter codes identifying the field:

| Code | Field |
| --- | --- |
| `T` | Title |
| `U` | User name |
| `P` | Password |
| `A` | URL |
| `N` | Notes |
| `I` | UUID |
| `O` | Other custom strings *(KeePass 2.x only)* |

The *Text* part is the [search string](https://keepass.info/help/base/search.html) that describes the text(s) that must occur in the specified field of an entry to match.

If multiple entries match the specified search criterion, the first entry will be used. To avoid ambiguity, an entry can be identified by its UUID, which is unique. Example: `{REF:P@I:46C9B1FFBD4ABC4BBB260C6190BAD20C}` would insert the password of the entry having 46C9B1FFBD4ABC4BBB260C6190BAD20C as UUID.

## Example

Let's assume you have two entries: one with title "Example Website" and one with "Example Forum", and you want to insert the user name of the website account into the URL of the forum entry. Within the forum entry's URL, you could reference the user name like this: `https://forum.example.com/?user={REF:U@T:Example Website}`
