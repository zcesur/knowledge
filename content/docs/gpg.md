---
title: GnuPG
---
# [GnuPG](https://wiki.archlinux.org/index.php/GnuPG)

## Usage
### Encrypt and decrypt

#### Asymmetric

You need to [\#Import a public key](https://wiki.archlinux.org/index.php/GnuPG#Import_a_public_key) of a user before encrypting (options `--encrypt` or `-e`) a file or message to that recipient (options `--recipient` or `-r`). Additionally you need to [\#Create a key pair](https://wiki.archlinux.org/index.php/GnuPG#Create_a_key_pair) if you have not already done so.

To encrypt a file with the name _doc_, use:

```
$ gpg -r user-id -e doc
```

To decrypt (option `--decrypt` or `-d`) a file with the name _doc_.gpg encrypted with your public key, use:

```
$ gpg -o doc -d doc.gpg
```

_gpg_ will prompt you for your passphrase and then decrypt and write the data from _doc_.gpg to _doc_. If you omit the `-o` (`--output`) option, _gpg_ will write the decrypted data to stdout.

#### Symmetric

Symmetric encryption does not require the generation of a key pair and can be used to simply encrypt data with a passphrase. Simply use `--symmetric` or `-c` to perform symmetric encryption:

```
$ gpg -c doc
```

The following example:

* Encrypts `doc` with a symmetric cipher using a passphrase
* Uses the AES-256 cipher algorithm to encrypt the passphrase
* Uses the SHA-512 digest algorithm to mangle the passphrase
* Mangles the passphrase for 65536 iterations

```
$ gpg -c --s2k-cipher-algo AES256 --s2k-digest-algo SHA512 --s2k-count 65536 doc
```

To decrypt a symmetrically encrypted `doc.gpg` using a passphrase and output decrypted contents into the same directory as `doc` do:

```
$ gpg -o doc -d doc.gpg
```

## gpg-agent

_gpg-agent_ is mostly used as daemon to request and cache the password for the keychain.

### Configuration

gpg-agent can be configured via `~/.gnupg/gpg-agent.conf` file. The configuration options are listed in [gpg-agent(1)](https://jlk.fjfi.cvut.cz/arch/manpages/man/gpg-agent.1). For example you can change cache ttl for unused keys:

```
default-cache-ttl 3600
```

### Reload the agent

After changing the configuration, reload the agent using _gpg-connect-agent_:

```
$ gpg-connect-agent reloadagent /bye
```

The command should print `OK`.
