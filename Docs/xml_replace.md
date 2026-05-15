<!-- Source: https://keepass.info/help/v2/xml_replace.html -->

# XML Replace

About the XML replacement functionality.

- [General Information](#info)
- [Examples](#ex):
  - [Replace text in all entry titles and notes](#repstr)
  - [Replace all HTTP URLs by HTTPS URLs](#tohttps)
  - [Replace group icons](#repgrpicon)
  - [Delete entry strings by name](#delstr)
  - [Delete entry attachments by name extension](#delbinext)
  - [Reset background colors](#resetbgcolor)
  - Auto-Type:
    - [Disable auto-type for entries with empty fields](#atdisable)
    - [Convert `{DELAY=` to upper-case](#atdelayupper)
    - [Prepend `{DELAY=50}` to all sequences without a `{DELAY=`](#ataddstddelay)
    - [Change `{DELAY=` values](#atmodstddelay)
    - [Remove `{DELAY=x}` from all sequences](#atrmvstddelay)
    - [Reset default sequences that contain `{DELAY=`](#atresetifstddelay)
    - [Add an auto-type association to all entries](#ataddassoc)
  - [Copy entry URLs into title fields](#copyurls)
  - [Copy entry titles into empty user name fields](#copytitles)
  - [Ensure first line is not empty](#firstnotempty)

## General Information

XML Replace is a powerful feature that modifies a database by manipulating its XML representation.

It creates a [KeePass 2.x XML](../../help/kb/kdbx.html#xml) DOM of the current database in memory, performs the operation specified by the user (e.g. remove nodes or replace text), loads the modified XML tree, and merges the current database with the modified database.

This is a feature for experts. Use with caution!

XML Replace can be invoked via 'Tools' → 'Database Tools' → 'XML Replace'.

Information about XPath and regular expressions can be found on the '[Search](../base/search.html)' help page.

KeePass protects history entries; XML Replace cannot be used to modify these. Furthermore, any changes to database properties (database name/description, etc.) may be ignored.

## Examples

| Replace text in all entry titles and notes |
| --- |
| Select nodes: | `//Entry/String[(Key = 'Title') or (Key = 'Notes')]/Value` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `TheTextToFind` |
| Replace with: | `TheReplacement` |
| Within all entry titles and notes, this replaces all occurrences of `TheTextToFind` by `TheReplacement`. |

| Replace all HTTP URLs by HTTPS URLs |
| --- |
| Select nodes: | `//Entry/String[Key = 'URL']/Value` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `^http:` |
| Replace with: | `https:` |
| Options: | ☑ Regular expressions |
| Within all entry URL fields, this replaces all HTTP URLs by HTTPS URLs. |

| Replace group icons |
| --- |
| Select nodes: | `//Group/IconID` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `^48$` |
| Replace with: | `36` |
| Options: | ☑ Regular expressions |
| This assigns the ZIP package icon to all groups that currently have a closed folder as icon. All icon IDs can be found in the icon picker dialog. |

| Delete entry strings by name |
| --- |
| Select nodes: | `//Entry/String[Key = 'TheName']` |
| Action: | Remove nodes |
| Removes all entry strings named `TheName`. |

| Delete entry attachments by name extension |
| --- |
| Select nodes: | `//Entry/Binary/Key[(string-length(.) >= 4) and (substring(., string-length(.) - 3) = '.jpg')]/..` |
| Action: | Remove nodes |
| Removes all entry attachments that have a name ending in '.jpg'. |

| Reset background colors |
| --- |
| Select nodes: | `//Entry/BackgroundColor` |
| Action: | Remove nodes |
| Sets the background color of all entries to the default (transparent/alternating). |

| Disable auto-type for entries with empty fields |
| --- |
| Select nodes: | `//Entry/String[((Key = 'UserName') or (Key = 'Password')) and (Value = '')]/../AutoType/Enabled` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `True` |
| Replace with: | `False` |
| Disables auto-type for all entries that have an empty user name field or an empty password field. |

| Convert `{DELAY=` to upper-case |
| --- |
| Select nodes: | `//DefaultSequence \| //KeystrokeSequence` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `{DELAY=` |
| Replace with: | `{DELAY=` |
| Converts all `{DELAY=` codes within auto-type sequence overrides and associations to upper-case (by default the case sensitivity option is turned off, thus the 'Find what' text matches all cases). In KeePass 2.x, placeholders are case-insensitive. However, this XML Replace operation may be useful as preparation for the following example (which matches `{DELAY=` in a case-sensitive way). |

| Prepend `{DELAY=50}` to all sequences without a `{DELAY=` |
| --- |
| Select nodes: | `(//DefaultSequence \| //KeystrokeSequence)[not(contains(., '{DELAY=')) and (. != '')]` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `^(.*)$` |
| Replace with: | `{DELAY=50}$1` |
| Options: | ☑ Regular expressions |
| Prepends a `{DELAY=50}` to all auto-type sequence overrides and associations that do not contain any `{DELAY=` already and are not empty. Note that the node selection is case-sensitive (independent of the data case sensitivity option), thus you need to ensure that all `{DELAY=` codes are upper-case before performing this operation. This can e.g. be done using the XML Replace operation mentioned [above](#atdelayupper). |

| Change `{DELAY=` values |
| --- |
| Select nodes: | `//DefaultSequence \| //KeystrokeSequence` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `\{DELAY=[\d\s]*\}` |
| Replace with: | `{DELAY=50}` |
| Options: | ☑ Regular expressions |
| Sets the values of all `{DELAY=` codes within auto-type sequence overrides and associations to 50. |

| Remove `{DELAY=x}` from all sequences |
| --- |
| Select nodes: | `//DefaultSequence \| //KeystrokeSequence` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `\{DELAY=[\d\s]*\}` |
| Replace with: | *(Leave empty)* |
| Options: | ☑ Regular expressions |
| Removes all `{DELAY=x}` codes from all auto-type sequences. |

| Reset default sequences that contain `{DELAY=` |
| --- |
| Select nodes: | `//DefaultSequence[contains(., '{DELAY=')]` |
| Action: | Remove nodes |
| If a sequence has been specified in the field 'Override default sequence' (in the entry dialog) and it contains `{DELAY=`, the sequence is reset, i.e. the option 'Inherit default auto-type sequence from group' is activated. |

| Add an auto-type association to all entries |
| --- |
| Select nodes: | `//Entry/AutoType` |
| Action: | Replace data |
| Data: | Outer XML |
| Find what: | `</AutoType>\Z` |
| Replace with: | `<Association><Window>* - Notepad</Window><KeystrokeSequence>{PASSWORD}</KeystrokeSequence></Association></AutoType>` |
| Options: | ☑ Regular expressions |
| Adds an auto-type association to all entries: the window title '`* - Notepad`' is associated with the sequence '`{PASSWORD}`'. |

| Copy entry URLs into title fields |
| --- |
| Select nodes: | `//Entry` |
| Action: | Replace data |
| Data: | Inner XML |
| Find what: | `(?s)(<Key>Title</Key>\s*)(<Value>.*?</Value>\|<Value\s*/>)(.*?<Key>URL</Key>\s*)(<Value>.*?</Value>\|<Value\s*/>)` |
| Replace with: | `$1$4$3$4` |
| Options: | ☑ Case-sensitive ☑ Regular expressions |
| Copies the entry URL into the title field of the entry (overwriting any existing data in the title field). If you want the entry URL to be copied only if the title field is empty, use the following for 'Select nodes': `//Entry/String[(Key = 'Title') and (Value = '')]/..` |

| Copy entry titles into empty user name fields |
| --- |
| Select nodes: | `//Entry/String[(Key = 'UserName') and (Value = '')]/..` |
| Action: | Replace data |
| Data: | Inner XML |
| Find what: | `(?s)(<Key>Title</Key>\s*<Value>)(.*?)(</Value>.*?<Key>UserName</Key>\s*)(<Value></Value>\|<Value\s*/>)` |
| Replace with: | `$1$2$3<Value>$2</Value>` |
| Options: | ☑ Case-sensitive ☑ Regular expressions |
| Copies the entry title into the user name field of the entry, if this field is empty. |

| Ensure first line is not empty |
| --- |
| Select nodes: | `//Entry/String/Value` |
| Action: | Replace data |
| Data: | Inner text |
| Find what: | `(?s)^(\r?\n)` |
| Replace with: | `--$1` |
| Options: | ☑ Regular expressions |
| For all multi-line fields, this inserts '`--`' into the first line of the field value, if this line is empty and the value has at least two lines. Example: Sample data is replaced by -- Sample data |
