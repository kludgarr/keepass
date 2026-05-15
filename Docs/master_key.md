<!-- Source: https://keepass.info/help/base/keys.html -->

# Master Key

Details about components of a master key.

- [Master Password](https://keepass.info/help/base/keys.html#password)
- [Key File](https://keepass.info/help/base/keys.html#keyfiles)
- [Windows User Account](https://keepass.info/help/base/keys.html#winuser)
- [For Administrators: Specifying Minimum Properties of Master Keys](https://keepass.info/help/base/keys.html#pwmin)

Your KeePass database file is encrypted using a master key. This master key can consist of multiple components: a master password, a key file and/or a key that is protected using the current Windows user account.

For opening a database file, *all* components of the master key are required.

If you forget/lose any of the master key components (or forget the composition), all data stored in the database is lost. There is no backdoor and no universal key that can open your database.

## Master Password

If you use a master password, you only have to remember one password or passphrase (which should be good!) to open your database.

KeePass features a protection against brute-force and dictionary attacks; see the [security](https://keepass.info/help/base/security.html#secdictprotect) help page for details.

## Key File

A key file is a file that contains a key (and possibly additional data, e.g. a hash that allows to verify the integrity of the key). The file extension typically is 'keyx' or 'key'.

A key file must not be modified, otherwise you cannot open your database anymore. If you want to use a different key file, open the dialog for changing the master key (via 'File' → 'Change Master Key') and create/select the new key file.

**Two-factor protection.** A key file is something that you must *have* in order to be able to open the database (in contrast to a master password, which you must *know*). If you use both a key file and a master password, you have a two-factor protection: possession and knowledge.

**Location.** As mentioned above, the idea of a key file is that you *have* something. If an attacker obtains both your database file and your key file, then the key file provides no protection. Therefore, the two files must be stored in different locations. For example, you could store the key file on a separate USB stick.

**Hiding the location.** The key file *content* must be kept secret, not its location (file path/name). Trying to hide the key file (e.g. by storing it among a thousand other files, in the hope that an attacker does not know which file is the correct one) typically does not increase the security, because it is easy to find out the correct file (e.g. by inspecting the last access times of files, lists of recently used files of the operating system, file system auditing logs, anti-virus software logs, etc.).

KeePass has an option for remembering the paths of key files, which is turned on by default; turning it off typically just decreases the usability without increasing the security. This option only affects KeePass itself (i.e. turning it off does not prevent the operating system or other software from remembering the paths). If you only want to prevent a key file from appearing in the recently used files list of Windows (which does not really affect the security) after selecting it in KeePass, consider turning on the option for entering the master key on a [secure desktop](https://keepass.info/help/base/security.html#secdesktop) (KeePass will then show a simpler key file selection dialog that does not add the file to the recently used files list of Windows).

**Backup.** You should create a backup of your key file (onto an independent data storage device). If your key file is an XML file (which is the default), you can also create a backup on paper (KeePass 2.x provides a command for printing a key file backup in the menu 'File' → 'Print'). In any case, the backup should be stored in a secure location, where only you and possibly a few other people that you trust have access to. More details about backing up a key file can be found in the [ABP FAQ](https://abp-keepass.sourceforge.net/FAQ.html).

**Formats.** KeePass supports the following key file formats:

- **XML (recommended, default).** There is an XML format for key files. KeePass 2.x uses this format by default, i.e. when creating a key file in the master key dialog, an XML key file is created. The syntax and the semantics of the XML format allow to detect certain corruptions (especially such caused by faulty hardware or transfer problems), and a hash (in XML key files version 2.0 or higher) allows to verify the integrity of the key. This format is resistant to most encoding and new-line character changes (which is useful for instance when the user is opening and saving the key file or when transferring it from/to a server). Such a key file can be printed (as a backup on paper), and comments can be added in the file (with the usual XML syntax: `<!-- ... -->`). It is the most flexible format; new features can be added easily in the future.
- **32 bytes.** If the key file contains exactly 32 bytes, these are used as a 256-bit cryptographic key. This format requires the least disk space.
- **Hexadecimal.** If the key file contains exactly 64 hexadecimal characters (0-9 and A-F, in UTF-8/ASCII encoding, one line, no spaces), these are decoded to a 256-bit cryptographic key.
- **Hashed.** If a key file does not match any of the formats above, its content is hashed using a cryptographic hash function in order to build a key (typically a 256-bit key with SHA-256). This allows to use arbitrary files as key files.

**Reuse.** You can use one key file for multiple database files. This can be convenient, but please keep in mind that when an attacker obtains your key file, you have to change the master keys of all database files protected with this key file.

## Windows User Account

## For Administrators: Specifying Minimum Properties of Master Keys

Administrators can specify a minimum length and/or the minimum estimated quality that master passwords must have in order to be accepted. You can tell KeePass to check these two minimum requirements by adding/editing appropriate definitions in the [INI/XML configuration file](https://keepass.info/help/base/configuration.html).

| Flag (Hex) | Flag (Dec) | Description |
| --- | --- | --- |
| 0x0 | 0 | Don't force any states (default). |
| 0x1 | 1 | Enable password. |
| 0x2 | 2 | Enable key file. |
| 0x4 | 4 | Enable user account. |
| 0x8 | 8 | Enable 'hide password' button. |
| 0x100 | 256 | Disable password. |
| 0x200 | 512 | Disable key file. |
| 0x400 | 1024 | Disable user account. |
| 0x800 | 2048 | Disable 'hide password' button. |
| 0x10000 | 65536 | Check password. |
| 0x20000 | 131072 | Check key file. |
| 0x40000 | 262144 | Check user account. |
| 0x80000 | 524288 | Check 'hide password' option/button. |
| 0x1000000 | 16777216 | Uncheck password. |
| 0x2000000 | 33554432 | Uncheck key file. |
| 0x4000000 | 67108864 | Uncheck user account. |
| 0x8000000 | 134217728 | Uncheck 'hide password' option/button. |
