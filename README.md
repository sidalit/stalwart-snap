<div align="center">

<img src="snap/gui/stalwart.png" alt="Stalwart logo" width="128" />

# Stalwart

**Run your own mail and collaboration server.**

[![stalwart](https://snapcraft.io/stalwart/badge.svg)](https://snapcraft.io/stalwart)
[![Base: core26](https://img.shields.io/badge/Base-core26-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core26)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architectures: amd64, arm64](https://img.shields.io/badge/Architectures-amd64%20%7C%20arm64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for [Stalwart](https://github.com/stalwartlabs/stalwart), with SMTP, IMAP, JMAP, POP3, ManageSieve, CalDAV, CardDAV, WebDAV and a web administration interface.

</div>

---

## Getting Started

<a href="https://snapcraft.io/stalwart"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

```bash
sudo snap install stalwart
sudo cat /var/snap/stalwart/common/credentials/bootstrap-password
```

Open [http://127.0.0.1:8080/admin](http://127.0.0.1:8080/admin) and sign in as `admin` with the temporary password. The first start downloads the official WebUI from GitHub. The setup wizard listens on all interfaces over plain HTTP, so block port `8080` at the firewall until setup is complete. For a remote server, run `ssh -L 8080:127.0.0.1:8080 user@server`, then open the same local URL instead of sending the password across the network.

Complete the wizard with your mail hostname and domain. Keep the default storage path `/var/lib/stalwart/` and log path `/var/log/stalwart/`. Save the permanent administrator credentials displayed at the end, then restart:

```bash
sudo snap restart stalwart.daemon
```

The temporary account is removed and normal listeners become accessible on the network. Sign in at `https://<mail-hostname>/admin` with the permanent credentials, and complete [DNS and certificate setup](https://stalw.art/docs/install/).

## Configure

| Setting | Accepted values | Default |
| --- | --- | --- |
| `bootstrap-port` | Integer `1`–`65535`, without leading zeros | `8080` |

```bash
sudo snap get stalwart
sudo snap set stalwart bootstrap-port=8081
```

Changes restart a running service. `bootstrap-port` applies only to the setup wizard, which listens on all interfaces. Configure normal listeners and TLS in the web interface, then restart the service. The normal defaults listen on all interfaces:

| Protocol | TCP port | Transport |
| --- | --- | --- |
| SMTP delivery | `25` | STARTTLS |
| SMTP submission | `465` | TLS |
| IMAP | `993` | TLS |
| POP3 | `995` | TLS |
| ManageSieve | `4190` | STARTTLS |
| HTTPS, JMAP, WebDAV and administration | `443` | TLS |
| HTTP and administration | `8080` | Plain HTTP |

Restrict or disable plain HTTP for public deployments. Configure ACME or upload certificates through the web interface; local certificate files must be under `/var/snap/stalwart/common`.

Persistent configuration, mail and logs are under `/var/snap/stalwart/common`: `config.json`, `data/` and `logs/`. Back up external stores separately; stop the service before backing up local databases:

```bash
sudo snap stop stalwart.daemon
sudo snap save stalwart
sudo snap start stalwart.daemon
```

## Limitations

- Community Edition; Enterprise and FoundationDB are not included. `amd64` requires an `x86-64-v2` CPU.
- Public delivery requires DNS, reachable ports, valid TLS and suitable IP reputation. Host firewalls, DNS records and ISP port `25` blocks remain your responsibility.
- Setup requires outbound HTTPS to download the official WebUI. Its cached resource URL is managed through Stalwart's **Application** settings and survives snap updates. Spam rules and GeoIP data also use network downloads.
- `snap revert` does not roll back shared databases. Back up before upgrades and follow [upstream migration instructions](https://stalw.art/docs/install/upgrade/).
- Host mail spools, home directories and external certificate directories are outside confinement. Keep local files under the snap's persistent paths.

## Build

```bash
git clone https://github.com/sidalit/stalwart-snap.git
cd stalwart-snap
sudo snap install snapcraft --classic
snapcraft pack
```

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/stalwart-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/stalwartlabs/stalwart/issues).

## License

Snap packaging: [MIT](LICENSE).
