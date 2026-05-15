<!-- Source: https://keepass.info/help/base/cmdline.html -->

# Command Line Options

Command line options to automate KeePass tasks.

- [General](https://keepass.info/help/base/cmdline.html#gen)
- [Usage Examples](https://keepass.info/help/base/cmdline.html#ex)
- [Starting KeePass using a Batch File](https://keepass.info/help/base/cmdline.html#batch)
- [Closing/Locking KeePass using a Batch File](https://keepass.info/help/base/cmdline.html#close)
- [Editing URL Overrides (2.x)](https://keepass.info/help/base/cmdline.html#urloverride)

## General

You can pass a file path in the command line in order to tell KeePass to open this file immediately after startup.

Additionally, you can specify the password and/or key file location for this database. Switches can be either prefixed using a minus (`-`) or two minus characters (`--`). On Windows, a slash (`/`) is another alternative. The prefixes are equivalent; it doesn't matter which one you use.

**Database file.** The database file location is passed as argument. Only one database file is allowed. If the path contains a space, it must be enclosed in quotes (").

**Password.** Passwords can be passed using the `-pw:` option. In order to pass 'abc' as password, you would add the following argument to the command line: `-pw:abc`. Note that there must be no space between the '`:`' and the password. If your password contains a space, you must enclose it in quotes. For example: `-pw:"My secret password"`.

Using the `-pw:` option is not recommended, due to security reasons (the operating system allows reading the command line options of other applications).

The `-pw-enc:` parameter is similar to `-pw:`, but it requires the password to be encrypted. Encrypted passwords can be generated using the [`{PASSWORD_ENC}`](https://keepass.info/help/base/placeholders.html#passwordenc) placeholder.

When passing the `-pw-stdin` option, KeePass reads the password from the StdIn stream. This option is intended for programmatically passing the password to KeePass. For entering the password by hand, it is recommended to use the normal master key dialog instead (because in this dialog the password is hidden by bullets/asterisks and it is encrypted by the process memory protection).

**Key file/provider.** For supplying the key file path or the name of the key provider plugin, the `-keyfile:` parameter exists. The same rules as above apply, just that you specify the key file/provider, e.g. `-keyfile:D:\pwsafe.key`. You also need to quote the value, if it contains a space, tab or other whitespace characters.

**Preselection.** In order to just preselect a key file/provider, use the `-preselect:` option. For example, if you lock your database with a password *and* a key file, but just want to type in the password (so, without selecting the key file manually), your command line could look like this:

```
KeePass.exe "C:\My Documents\Database.kdbx" -preselect:C:\pwsafe.key
```

KeePass will then show a prompt for the master key of the database, in whose key file/provider list the `C:\pwsafe.key` file is already selected. When using the `-preselect:` parameter, KeePass by default activates the key file/provider option and sets the focus to the password edit window.

Note the difference! The `-preselect:` parameter just preselects the key file/provider in the master key dialog for you. In contrast, the `-keyfile:` parameter does not prompt you for the (maybe missing) password.

**Other.** The `-minimize` command line option makes KeePass start up minimized. This option may not work when KeePass runs on Mono (due to a bug in Mono).

The `-auto-type` command line option makes other already opened KeePass instances perform a global auto-type.

The order of the arguments is arbitrary.

## Usage Examples

Open the database file *'C:\My Documents\Database.kdbx'* (KeePass will prompt you for the password and/or key file location):

```
KeePass.exe "C:\My Documents\Database.kdbx"
```

If you got a database that is locked with a password 'abc', you could open it like this:

```
KeePass.exe "C:\My Documents\DatabaseWithPw.kdbx" -pw:abc
```

If your USB stick always mounts to drive F: and you've locked your database with a key file on the USB stick, you could open your database as follows:

```
KeePass.exe "C:\My Documents\DatabaseWithFile.kdbx" -keyfile:F:\pwsafe.key
```

If you've locked your database using a password *and* a key file, you can combine the two switches and open your database as follows:

```
KeePass.exe "C:\My Documents\DatabaseWithTwo.kdbx" -pw:abc -keyfile:F:\pwsafe.key
```

You have locked your database using a password *and* a key file, but only want to have the key file preselected (i.e. you want to get prompted for the password), your command line would look like this:

```
KeePass.exe "C:\My Documents\DatabaseWithTwo.kdbx" -preselect:F:\pwsafe.key
```

## Starting KeePass using a Batch File

Batch files can be used to start KeePass. Mostly you want to specify some of the parameters listed above. You can theoretically simply put the command line (i.e. application path and parameters) into the batch file, but this is not recommended as the command window will stay open until KeePass is closed. The following method is recommended instead:

```
START "" KeePass.exe ..\Database.kdbx -pw:MySecretPw
```

This `START` command will run KeePass (which opens the `..\Database.kdbx` file using `MySecretPw` as password). KeePass is assumed to be in the same directory (working directory) as the batch file, otherwise you need to specify a different path.

`START` executes the given command line and immediately exits, i.e. it doesn't wait until the application is terminated. Consequently, the command window will disappear after KeePass has been started.

Please note the two quotes (`"`) after the `START` command. These quotes are required if the application path contains quotes (in the example above, the quotes could also be removed). If you want to learn more about the `START` command syntax, type `START /?` into the command window.

## Closing/Locking KeePass using a Batch File

To close all currently running KeePass instances, call `KeePass.exe` with the `'--exit-all'` parameter:

```
KeePass.exe --exit-all
```

All KeePass windows will attempt to close. If a database has been modified, KeePass will ask you whether you want to save or not. If you wish to save in any case (i.e. a forced exit without any confirmation dialog), enable the *'Automatically save database on exit and workspace locking'* option in *'Tools' → 'Options' → tab 'Advanced'*.

The KeePass instance that has been created by the command above is not visible (i.e. it does not show a main window) and will immediately terminate after sending close requests to the other instances.

The `--lock-all` and `--unlock-all` command line options lock/unlock the workspaces of all other KeePass instances.

## Editing URL Overrides (2.x)

KeePass 2.x supports the following command line options for editing [URL overrides](https://keepass.info/help/base/autourl.html#override):

- `-add-urloverride`: Adds a URL override for a specific scheme. Specify the scheme using the '`-scheme:`' command line parameter and the override using the '`-value:`' command line parameter. If the URL override should be enabled, additionally pass the '`-activate`' command line option.
- `-remove-urloverride`: Removes a URL override for a specific scheme. Specify the scheme using the '`-scheme:`' command line parameter and the override using the '`-value:`' command line parameter.
- `-set-urloverride`: The value of this command line parameter (not the '`-value:`' command line parameter) is saved as override for all entry URLs.
- `-get-urloverride`: Saves the current override for all entry URLs to the file '`%TEMP%\KeePass_UrlOverride.tmp`' (INI format).
- `-clear-urloverride`: Removes the override for all entry URLs.

URL overrides are stored in the [enforced configuration file](https://keepass.info/help/kb/config_enf.html). For each of the command line options above except '`-get-urloverride`', a User Account Control dialog is displayed, if necessary.
