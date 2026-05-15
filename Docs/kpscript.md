<!-- Source: https://keepass.info/help/v2/scripting/kpscript.html -->

# KPScript - Single Command Operations

How to use KPScript with single command operations to perform simple database operations.

KPScript can be invoked using single commands. By passing the database location, its key, a command and eventually some parameters, simple operations like adding an entry can be performed. The syntax is very simple, no scripting knowledge is required. This method is ideal when you quickly want to do some small changes to the database. It is not recommended when you need to perform many operations, because for each command the database needs to be loaded from file, decrypted, modified, encrypted and written back to file.

Commands are specified by passing `-c:COMMAND` to KPScript, where `COMMAND` is the command to execute (see below for a list of available commands).

The database location is passed to KPScript by just passing it as a parameter, without any option prefix.

## Master Key

The master key for the database can be passed to KPScript using one of the following ways:

- *Command line parameters.* Using the `-pw:`, `-pw-enc:`, `-keyfile:` and `-useraccount` parameters. For example, to pass "Secret" as password, you'd give KPScript the following parameter: `-pw:Secret`. If the password contains spaces or other special characters, it must be enclosed in quotes: `-pw:"My Top Secret Password"`. For `-pw-enc:`, see the [`{PASSWORD_ENC}`](https://keepass.info/help/base/placeholders.html#passwordenc) placeholder. The `-keyfile:` parameter can specify the key file location. If `-useraccount` is passed to KPScript, the user account credentials of the currently logged on user are used, otherwise not.
- *Reading from StdIn.* If you pass `-keyprompt` to KPScript, it will read the password, the key file path and the user account flag from the StdIn stream. This option is intended for programmatically passing the key to KPScript. For entering the password by hand, it is recommended to use the normal master key dialog instead (because in this dialog the password is hidden by bullets/asterisks and it is encrypted by the process memory protection), see `-guikeyprompt`.
- *Entering interactively using graphical user interface.* If you pass `-guikeyprompt` to KPScript, it will prompt you for the key using the normal master key dialog of KeePass.

## Available Commands

Please note that commands are added incrementally based on user requests. If you are missing a command, please let the KeePass team know and it will be added to the next release of KPScript.

Currently, the following commands are available:

- [ListGroups](https://keepass.info/help/v2_dev/scr_sc_index.html#listgroups)
- [ListEntries](https://keepass.info/help/v2_dev/scr_sc_index.html#listentries)
- [GetEntryString](https://keepass.info/help/v2_dev/scr_sc_index.html#getentrystring)
- [AddEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#addentry)
- [EditEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#editentry)
- [MoveEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#moveentry)
- [DeleteEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#deleteentry)
- [DeleteAllEntries](https://keepass.info/help/v2_dev/scr_sc_index.html#deleteallentries)
- [Import](https://keepass.info/help/v2_dev/scr_sc_index.html#import)
- [Export](https://keepass.info/help/v2_dev/scr_sc_index.html#export)
- [Sync](https://keepass.info/help/v2_dev/scr_sc_index.html#sync)
- [ChangeMasterKey](https://keepass.info/help/v2_dev/scr_sc_index.html#changemasterkey)
- [DetachBins](https://keepass.info/help/v2_dev/scr_sc_index.html#detachbins)
- [GenPw](https://keepass.info/help/v2_dev/scr_sc_index.html#genpw)
- [EstimateQuality](https://keepass.info/help/v2_dev/scr_sc_index.html#estimatequality)

**Command: ListGroups**

This command lists all groups in a format that easily machine-readable. The output is not intended to be printed/used directly. Usage example:

`KPScript -c:ListGroups "C:\KeePass\MyDb.kdbx" -pw:MyPassword` This will list all groups contained in the MyDb.kdbx database file.

**Command: ListEntries**

This command lists all entries in a format that easily machine-readable. The output is not intended to be printed/used directly. The entry identification syntax is exactly the same as in the [EditEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#editentry) command. Usage example:

`KPScript -c:ListEntries "C:\KeePass\MyDb.kdbx" -pw:MyPassword -keyfile:"C:\KeePass\MyDb.key"` Opens the MyDb.kdbx database using 'MyPassword' as password and the MyDb.key file as key file. It will output a list of all entries contained in the MyDb.kdbx database file.

**Command: GetEntryString**

Retrieves the value of an entry string field. The entry identification syntax is exactly the same as in the [EditEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#editentry) command. Additional command line parameters:

- `-Field:NAME` The field name can be specified using the '`-Field`' parameter. Supported field names are e.g. Title, UserName, Password, URL, Notes, etc.
- `-FailIfNotExists` If you pass the option '`-FailIfNotExists`' and the specified field does not exist, the operation is aborted and an error is returned.
- `-FailIfNoEntry` If you pass the option '`-FailIfNoEntry`' and no entry is found, KPScript terminates with an error.
- `-Spr` Spr-compiles the value of the field, i.e. [placeholders](https://keepass.info/help/base/placeholders.html) are replaced, [field references](https://keepass.info/help/base/fieldrefs.html) are resolved, etc.

Usage example:

`KPScript -c:GetEntryString "C:\KeePass\MyDb.kdbx" -pw:MyPassword -Field:UserName -ref-Title:"Demo Account"` Opens the MyDb.kdbx database using 'MyPassword' as password. It outputs the user names of all entries that have the title "Demo Account".

**Command: AddEntry**

This command adds an entry to the database. To specify the entry details, use the standard string field identifiers as parameter names and their values for the contents. Supported standard string fields are: Title, UserName, Password, URL, and Notes. Usage examples:

`KPScript -c:AddEntry "C:\KeePass\MyDb.kdbx" -pw:MyPw -Title:"New entry title"`

`KPScript -c:AddEntry "C:\KeePass\MyDb.kdbx" -pw:MyPw -Title:SomeWebsite -UserName:Don -Password:pao5j3eg -URL:https://example.com/`

Additional command line parameters:

- `-GroupName:NAME` The `-GroupName:` parameter can be used to specify the group in which the entry is created. For searching, KPScript performs a pre-order traversal and uses the first matching group (the name is case-sensitive). If no group with the specified name is found, it will be created in the root group.
- `-GroupPath:PATH` The full path of the group can be specified using the `-GroupPath:` parameter (use '`/`' as separator). If you do not specify a group name or path, the entry will be created in the root group.
- `-setx-Icon:ID` Set the icon of the entry to the standard icon having index *ID*.
- `-setx-CustomIcon:ID` Set the icon of the entry to the custom icon having index *ID*.
- `-setx-Expires:VALUE` Sets whether the entry expires or not. *VALUE* must be either `true` or `false`.
- `-setx-ExpiryTime:VALUE` Sets the expiry date/time of the entry.

Usage example:

`KPScript -c:AddEntry "C:\KeePass\MyDb.kdbx" -pw:MyPw -Title:"My Provider" -GroupName:"Internet Sites"`

**Command: EditEntry**

This command edits existing entries.

Use one or more of the following parameters to identify the entries to be edited; all of the specified conditions must match:

- `-ref-FIELDNAME:FIELDVALUE` The string field *FIELDNAME* must have the value *FIELDVALUE*. If the value is enclosed in '`//`', it is treated as a https://msdn.microsoft.com/en-us/library/az24scfc.aspx https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference [regular expression](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference), which must occur in the entry field for the entry to match. For example, `-ref-Title:"//Test\d\d//"` matches every entry whose title contains 'Test' followed by at least two digits.
- `-refx-UUID:VALUE` The UUID of the entry must be *VALUE*.
- `-refx-Tags:VALUE` The entry must have the specified tags. Multiple tags can be separated using commas '`,`'.
- `-refx-Expires:VALUE` *VALUE* must be `true` or `false`. This parameter allows to specify whether the entry expires sometime (i.e. whether the 'Expires' checkbox is checked, independent of the expiry time).
- `-refx-Expired:VALUE` *VALUE* must be `true` or `false`. This parameter allows to specify whether the entry has expired (i.e. whether the 'Expires' checkbox is checked and the expiry time is not in the future).
- `-refx-Group:VALUE` The name of the parent group of the entry must be *VALUE*.
- `-refx-GroupPath:VALUE` The full path of the parent group of the entry must be *VALUE*. Use '`/`' as group separator in the path.
- `-refx-All` Matches all entries.

Use one or more of the following parameters to specify how the entry should be edited:

- `-set-FIELDNAME:FIELDVALUE` Sets the string field *FIELDNAME* of the entry to the value *FIELDVALUE*.
- `-setx-Icon:ID` Set the icon of the entry to the standard icon having index *ID*.
- `-setx-CustomIcon:ID` Set the icon of the entry to the custom icon having index *ID*.
- `-setx-Expires:VALUE` Sets whether the entry expires or not. *VALUE* must be either `true` or `false`.
- `-setx-ExpiryTime:VALUE` Sets the expiry date/time of the entry.

Usage examples:

`KPScript -c:EditEntry "C:\KeePass\MyDb.kdbx" -pw:MyPw -ref-Title:"Existing entry title" -set-UserName:"New user name"`

`KPScript -c:EditEntry "C:\KeePass\MyDb.kdbx" -pw:MyPw -ref-UserName:MyName -set-UserName:NewName -set-Password:"Top Secret"`

If you additionally pass `-CreateBackup`, KPScript will first create backups of entries before modifying them.

**Command: MoveEntry**

This command moves one or more existing entries. The entry identification syntax is exactly the same as in the [EditEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#editentry) command.

- `-GroupPath:PATH` The target group can be specified using the `-GroupPath:` parameter. '`/`' must be used as separator (e.g. `-GroupPath:Internet/eMail` moves the specified entries to the subgroup 'eMail' of the subgroup 'Internet').
- `-GroupName:NAME` The `-GroupName:` parameter can be used (see the [AddEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#addentry) command for details).

**Command: DeleteEntry**

This command deletes one or more existing entries. The entry identification syntax is exactly the same as in the [EditEntry](https://keepass.info/help/v2_dev/scr_sc_index.html#editentry) command.

**Command: DeleteAllEntries**

This command deletes all entries (in all subgroups).

**Command: Import**

This command imports a file into the database.

- `-Format:NAME` The format is specified by setting the "-Format" parameter (see names in the import dialog of KeePass).
- `-File:PATH` The file to import to is specified using the "-File" parameter.
- `-MM:VALUE` If the format supports UUIDs, the behavior for groups/entries that exist in both the current database and the import file can be specified using the optional "-MM" parameter. Possible values are "CreateNewUuids", "KeepExisting", "OverwriteExisting", "OverwriteIfNewer", and "Sync". By default, new UUIDs are created.
- `-imp_*:VALUE` For encrypted import files, by default the master key of the target database is used. However, it is also possible to specify a different master key, using the usual [master key command line parameters](https://keepass.info/help/v2_dev/scr_sc_index.html#masterkey) with the prefix '`-imp_`' (i.e. `-imp_pw:`, `-imp_pw-enc:`, `-imp_keyfile:`, `-imp_useraccount`, `-imp_keyprompt`, `-imp_guikeyprompt`).

Usage example:

`KPScript -c:Import "C:\KeePass\MyDb.kdbx" -pw:MyPw -Format:"KeePass XML (2.x)" -File:SourceFile.xml`

**Command: Export**

This command exports (parts of) the database.

- `-Format:NAME` The format is specified by setting the "-Format" parameter (see names in the export dialog of KeePass).
- `-OutFile:PATH` The file to export to is specified using the "-OutFile" parameter.
- `-GroupPath:PATH` If a specific group should be exported (instead of the whole database), specify the group using the "-GroupPath" parameter (use '`/`' as separator).
- `-XslFile:PATH` For the XSL transformation export module, the path of the XSL file can be passed using the "-XslFile" parameter.

Usage example:

`KPScript -c:Export "C:\KeePass\MyDb.kdbx" -pw:MyPw -Format:"KeePass XML (2.x)" -OutFile:TargetFile.xml`

**Command: Sync**

This command synchronizes the database with another one. The other database path has to be specified using the "-File" command line parameter. Usage example:

`KPScript -c:Sync -guikeyprompt "C:\Path\A.kdbx" -File:"C:\Path\B.kdbx"`

**Command: ChangeMasterKey**

This command changes the master key of the database. The new key values are specified using the standard options prefixed with 'new', i.e. `-newpw:`, `-newkeyfile:` and `-newuseraccount` (all are optional). Usage example:

`KPScript -c:ChangeMasterKey "C:\KeePass\MyDb.kdbx" -pw:MyPw -newpw:MyNewPw`

**Command: DetachBins**

This command saves all entry attachments (into the directory of the database) and removes them from the database. This might e.g. be useful when the database is too large to be opened on the current computer. Usage example:

`KPScript -c:DetachBins -guikeyprompt "C:\KeePass\MyDb.kdbx"`

**Command: GenPw**

Generates passwords.

- `-count:NUMBER` The number of passwords can be specified using the optional `-count:` parameter.
- `-profile:NAME` A password generator profile can be specified using the optional `-profile:` parameter (the names of all available profiles can be found in the password generator dialog).

Usage examples:

`KPScript -c:GenPw` Generates one password using the default generator profile.

`KPScript -c:GenPw -count:5 -profile:"Hex Key - 128-Bit (built-in)"` Generates five 128-bit hex passwords (when no translation is used).

**Command: EstimateQuality**

Estimates the [quality](https://keepass.info/help/kb/pw_quality_est.html) (in bits) of the password specified via the `-text:` parameter. Usage example:

`KPScript -c:EstimateQuality -text:MyTopSecretPassword`

## Console Character Encoding

If you observe garbled special characters in the output, please read the page [Console Character Encoding](https://keepass.info/help/kb/console_encoding.html).
