# HTTP Proxy Classic — releases

Windows installers for **HTTP Proxy Classic**, an HTTP/HTTPS traffic inspector
and debugger.

**[Download the latest release](../../releases/latest)**

Source code is maintained privately. This repository exists to host release
artifacts and the update policy the application reads.

---

## Installing

1. Download `http-proxy-classic-Setup-<version>.exe` from the
   [Releases](../../releases) page.
2. Run it. Windows SmartScreen may warn that the publisher is unrecognised —
   see [Code signing](#code-signing) below.
3. The installer lets you choose the installation directory and creates Start
   menu and desktop shortcuts.

To inspect HTTPS traffic the application generates its own certificate
authority and asks permission before adding it to your user certificate store.
Uninstalling offers to remove it again.

## Updates

The application checks this repository for new releases shortly after startup.

It never downloads anything without you choosing **Download update**, and never
installs anything without you choosing **Restart and install**. Both are in
**Settings → Updates**, along with a switch to turn automatic checking off
entirely.

Update checks are anonymous HTTPS requests to `github.com` and
`raw.githubusercontent.com`. Nothing about your captured traffic, your machine,
or your usage is transmitted.

## Beta builds expire

Builds on the **beta** channel carry a hard expiry date, currently
**31 December 2026**.

A beta build stops operating after that date and offers only an update path.
This keeps unsupported preview builds from staying in service indefinitely.
Your captures, rules, and settings are never deleted — installing a newer
release restores full function.

Stable releases have no expiry.

The application warns in-app for 14 days before a beta expires. If you are
running a beta near that date, update before it lapses.

## Code signing

Releases are currently **unsigned**. Windows SmartScreen will show a
"Windows protected your PC" prompt; choose *More info* → *Run anyway* if you
trust the source. Some antivirus products may also flag the installer, because
intercepting TLS is exactly the behaviour they are built to notice.

Every release manifest carries a SHA-512 checksum of the installer, and the
application verifies it before installing an update.

Code signing is planned. When it arrives the publisher identity will stay
constant, so updates will continue to work for existing installations.

---

## `policy/policy.json`

The application fetches this file to learn which versions are still supported.
It is read over HTTPS from `raw.githubusercontent.com` and is deliberately
fail-open: if it cannot be reached, or is malformed, the application keeps
working normally.

| Field | Meaning |
| --- | --- |
| `schema` | Format version. Clients ignore a file whose schema they do not recognise. |
| `stable.latestVersion` | Newest release on the stable channel. |
| `stable.minimumVersion` | Oldest stable version still supported. Anything below it is asked to update. |
| `beta.latestVersion` | Newest release on the beta channel. |
| `beta.minimumVersion` | Oldest beta version still supported. |
| `message` | Text shown to a client running an unsupported version. |
| `downloadUrl` | Where that client is sent to update. |
| `betaExpiryExtendedTo` | Optional ISO 8601 timestamp. Extends the expiry date baked into beta builds. It can only move the deadline **later**; a value earlier than the build's own deadline is ignored. `null` means no extension. |

Editing this file takes effect for online clients within roughly five minutes,
the cache lifetime of the raw content CDN. No rebuild or new release is needed.
