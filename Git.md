# Git

## Table of Contents

* [Summary](#Summary)
* [How It Works](#How-It-Works)
* [Helpful Links](#Helpful-Links)
* [Useful Tidbits](#Useful-Tidbits)
  * [`git-credential-store` - Helper to store credentials on disk](#git-credential-store---Helper-to-store-credentials-on-disk)
  * [`git-credential-cache` - Helper to temporarily store passwords in memory](#git-credential-cache---Helper-to-temporarily-store-passwords-in-memory)
* [Projects](#Projects)

## Summary

> Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals.

## How It Works

## Helpful Links

* [Git tutorial (`gittutorial`)](https://git-scm.com/docs/gittutorial)
* [Everyday Git (`giteveryday`)](https://git-scm.com/docs/giteveryday)

## Useful Tidbits

* ### [`git-credential-store` - Helper to store credentials on disk](https://git-scm.com/docs/git-credential-store)

  * Name: `git-credential-store`
  * Synopsis:
  
    ```bash
    git config credential.helper 'store [<options>]'
    ```

  * Description: helps store your passwords *unencrypted* on disk, protected **only** by filesystem permissions. If you require slightly more security on your credentials, you can use [`git-credential-cache`](#git-credential-cache---Helper-to-temporarily-store-passwords-in-memory), or something external that can integrate with `git` (e.g. [Git Credential Manager for Windows](https://github.com/microsoft/Git-Credential-Manager-for-Windows))

* ### [`git-credential-cache` - Helper to temporarily store passwords in memory](https://git-scm.com/docs/git-credential-cache)

  * Name: `git-credential-cache`
  * Synopsis:

    ```bash
    git config credential.helper 'cache [<options>]'
    ```

  * Description: caches credentials temporarily, in memory for future Git programs. The stored credentials never touch the disk, and are forgotten after a configurable timeout. The cache is accessible over a Unix domain socket, restricted to the current user by filesystem permissions.

## Projects
