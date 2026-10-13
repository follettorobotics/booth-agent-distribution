# Booth agent distribution

Signed update packages of the Folletto booth agent. Binaries only: there is no source here.
Booths read this repository by themselves; nothing here is edited by hand.

```
<model>/latest.json        what booths of that model are offered
<model>/latest.json.sig    its signature (ECDSA P-256 / SHA-256, base64 of the DER signature over the exact bytes)
release v<X.Y.Z>-<model>   the package: booth-agent-<model>-<X.Y.Z>.zip
```

| folder | booths |
|---|---|
| `g2donut/` | Gen 2 donut booth: Hanwha robot, Eversys, donut box lift on station 4 |

A booth checks `https://raw.githubusercontent.com/follettorobotics/booth-agent-distribution/main/<model>/latest.json`, verifies the
signature with the public keys built into its exe, downloads the zip the manifest names, checks its sha256, and
installs it when staff allow it or the booth has been idle. A booth never installs a lower version, another
model's package, or anything the signature does not cover.

## Rules

- **Never edit `latest.json` or `latest.json.sig`.** Any change breaks the signature and every booth of that model
  stops seeing updates. `.gitattributes` (`* -text`) keeps git from changing a byte of them.
- **Never replace a release asset** of a version `latest.json` has named: booths pin its sha256.
- Releases are made only by the release workflow of the private source repository, from a tag `v<X.Y.Z>-<model>`.

## Withdrawing a release

Revert the commit that published it. The previous two files come back byte for byte and still verify, so booths
are offered the previous version again and discard a withdrawn package they had already downloaded.

```
git clone https://github.com/follettorobotics/booth-agent-distribution.git && cd booth-agent-distribution
git log --oneline -- <model>/latest.json          # find "Release v<X.Y.Z>-<model>"
git revert <that commit>
git push origin main
```

Then delete the withdrawn release (asset and tag) here. Only the newest release of a model can be withdrawn this
way. A withdrawn version number is never used again: ship the fix under a higher number. Booths that already
installed the withdrawn version are not moved back by this; staff use "Back to previous version" on the booth, or
wait for the fixed release.

## Installing by hand

A new booth PC is set up by a Folletto engineer with the package from the release page plus the install kit of
the same version, which is not public. The unzipped package's `install.bat "<booth folder>"` runs the same helper
as an automatic update: backup, health check and rollback included.
