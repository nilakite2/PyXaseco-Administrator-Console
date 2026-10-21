# PyXaseco 1.3 Installation Guide

Install the Administrator Console once and manage multiple TrackMania Forever
servers on the same computer. Each server keeps its own controller, configuration,
Python environment and database. The complete controller source is included.

## Download

[Download PyXaseco Administrator Console for Windows and Linux](https://github.com/nilakite2/PyXaseco-Administrator-Console/releases/latest)

Open **Assets** on the release page to find the installers.

## Windows

1. Run **PyXasecoSetup.exe** and choose a shared-tools folder separate from your
   server folders. The Console includes its own desktop runtime; opening it does
   not require a separate Python installation.
2. Open **PyXaseco > Set up a server instance** from the Start Menu.
3. Choose **New server** to deploy the included dedicated server, or install
   PyXaseco for an existing dedicated-server installation. If PyXaseco is already
   installed, use **Add server > Register an existing PyXaseco server** instead.
4. Enter a unique instance name and folder. New servers require a new or empty
   folder. A server named `TechTest` receives `TechTest.exe` and `TechTest.bat`.
5. Python is detected automatically. Setup reuses compatible Python 3.12+ with
   venv/pip support, or offers the shared managed runtime on Windows if none is
   found. Manual selection and custom sources are under **Advanced settings**.
   Nothing is installed during detection. Setup creates an isolated environment
   and installs its libraries when the default library option is selected.
6. Enter XML-RPC access, MasterAdmin logins and your TMF server-account details.
   Give every instance distinct game, P2P and XML-RPC ports.
7. Configure the database and Basic/Advanced dedicated-server settings, select
   optional apps, then review and install.
8. Start the dedicated server using its named launcher. Start PyXaseco using
   **StartController.bat** in its `PyXaseco` folder. This uses the selected instance
   environment, not a system-wide Python default.

Existing dedicated files, maps and MatchSettings are not rewritten in PyXaseco-only
mode. Setup refuses to merge over an existing controller. Use **Services > Server
actions > Upgrade, rollback or uninstall** for controller upgrades or rollback.

### Register A Working Server

Choose **Add server > Register an existing PyXaseco server**, then its server or
controller folder. Python is detected from its running process or local environment
first, then registered/system locations. The XML-RPC endpoint is read without
displaying credentials. A unique dedicated executable is selected automatically;
ambiguous choices remain unset for explicit selection under **Advanced paths**.
Registration changes only the Console inventory, not server files or processes.
It does not enable a missing management bridge. Older controllers still require
the separate Console support update before writable management is possible.

Right-click a server in the sidebar or Dashboard for its actions. The same actions
are available under **Services > Server actions**. Start/Stop/Restart remain visible;
launchers, prerequisites, maintenance and service-plan options are in the menu.
Apps have a right-click menu and **App actions**; backups are under **File actions**.

**Hide sensitive information** controls both the local log viewer and configuration
display. It starts enabled and is saved per Console inventory, not in server settings.
Turn it off to read local passwords and unmasked logs; turn it on before sharing
screenshots. Switching back clears revealed text and reloads masked data. The
underlying files, runtime API and file permissions are unchanged. Password edits
still require the explicit **Replace this secret** checkbox.

Configuration initially opens `.env` when present, otherwise `config.toml`; other
settings/app TOML files remain available. Inherited environment values still take
precedence, so this is a file view, not a claim to show every effective runtime value.
Short/numeric passwords are masked in credential-labelled log fields, not globally
inside dates, public map IDs or memory addresses. Masking remains best-effort.

### Python And MariaDB

Windows setup includes the official Python archive and optional MariaDB installer.
Managed Python is shared by instances owned by the current operator without
replacing registered system Python installations. Controller libraries are downloaded
from the configured Python package index; their installation requires internet access.

Use the prerequisite check under setup's **Advanced settings**, or **Services >
Server actions > Python and database checks** for an existing instance.
Library repair installs pinned requirements only into an isolated instance environment,
never global Python. For a manually registered controller, create/select a venv first.

Reuse an existing MariaDB/MySQL service when available. **Install standalone MariaDB**
opens the official installer with administrator approval; no XAMPP is needed.
Choose its service and administrator authentication settings there. Existing database products
are not silently replaced or upgraded. Starting a database service is a separate
confirmed action.

For multiple servers, use one MariaDB service with a separate database and user per
instance. The configuration page offers three modes:

- **Configure only:** save connection settings without contacting the database.
- **Verify:** check that the supplied database user can connect.
- **Create:** create a new database, grant a new user access to that database only,
  and initialize controller tables. Existing database/user names are refused.

Create mode needs a database administrator account. Both database password fields
are optional: leave a field blank if that account has no password. The administrator
password is used once and is not saved or passed on the command line. A newly
created instance user uses exactly the supplied password, including an empty one.
MariaDB must allow the requested authentication; setup does not change existing
accounts or weaken the database server's authentication policy. Restrict passwordless
accounts to local access. The instance password is stored in its
restricted `.env`. Failed provisioning may leave a partially created *new* database
or user; setup does not delete these automatically. Inspect before retrying.

### Dedicated Server Settings

New-server setup writes `GameData/Config/dedicated_cfg.txt` on the new copy:

- **Basic:** comment, player/spectator limits and passwords, validation key,
  visibility, ladder limits, map downloads and voting.
- **Advanced:** P2P, replay saving, referee access, network settings, pack mask,
  blacklist/guestlist files and additional XML-RPC passwords.

The instance name sets the initial server name. XML-RPC remains local. Blank
Admin/User XML-RPC passwords are replaced with random values; SuperAdmin uses
the controller password. Existing-server mode leaves this configuration alone.

## Ubuntu / Debian

```sh
sudo apt install ./pyxaseco_1.3.0-1_amd64.deb
pyxaseco --help
pyxaseco payload-info
```

The package requires Python 3.12+, venv support and distribution aiohttp. It does
not replace OS Python. Shared tools live under `/usr/lib/pyxaseco`; create instances
in an operator-owned directory outside it. Controllers use their own source and
environment. Start them with `StartController.sh`.

For the optional desktop console:

```sh
sudo apt install python3-pyside6.qtwidgets python3-psutil
pyxaseco-console --setup
```

MariaDB and Python venv support can be installed through explicit prerequisite
actions using the OS package manager, with administrator approval. Alternatively
install `mariadb-server` using apt and configure its administrator access locally.
Unix-socket-only administrator authentication is not a TCP password login; select
an appropriate account rather than silently changing its authentication.

The supplied TMF executable is static 32-bit x86. This amd64 package needs a kernel
with 32-bit x86 execution support; it is not an ARM package. No Windows executables
or Windows prerequisite installers are included in the DEB.

Headless setup uses the same reviewed request as the GUI; see `SETUP.md` for private
JSON/CLI examples. Qt is not required for CLI use. Never pass passwords in arguments.

## Services And Maintenance

The Console supports two independent management modes:

- **Existing launchers / manual processes:** Start, Stop and Restart without creating
  OS services. Under Services choose **Configure existing launchers** to select the
  actual dedicated executable and its existing `.bat`/`.cmd` (Linux `.sh`) launcher.
  The controller can use registered Python directly or its own existing launcher.
  A matching dedicated launcher beside the registered executable is offered by default;
  older registrations pointing to a batch file are recognized when its matching
  executable exists. Custom paths/arguments can be reviewed and saved in Console
  preferences without editing server files. Only run trusted scripts: their existing
  commands execute under your account. Launcher changes after confirmation are refused.
- **Attached OS service plan:** control already-installed Windows SCM/systemd services.
  Generating/attaching definitions does not register services. Automatic/manual OS
  startup controls require a service plan; manual processes are not silently converted.

Process identification uses the complete executable path, controller script/config,
working directory and PID creation time, not a shared process name. Unreadable or
ambiguous matches block actions. Controller stop/restart uses its authenticated
management connection. Dedicated stop verifies ownership of the local XML-RPC port
before using XAseco's `StopServer` / `QuitGame` sequence. There is no forced-kill
fallback. Pair stop orders controller before dedicated; pair start reverses that order.
Failure cancels remaining steps and reports completed ones; it does not silently roll back.
Background status polling shares path-resolution results across installations within
each short-lived process snapshot. Explicit lifecycle actions still request a fresh
snapshot. Windows polling skips thread-status enumeration, and unchanged table cells
are not rebuilt on every response.

Closing the Console does not stop launched processes. A batch file that opens its own
console windows retains that behavior; those wrappers are not forcibly killed.

Older controllers need the Console compatibility update and a separately enabled
management connection. Existing installations can be registered while running;
registration alone does not install/enable that connection. Service-account/reboot
acceptance remains separate. Nothing starts automatically at setup finish.

Process inspection uses [psutil](https://psutil.io/), bundled with the Windows Console
and optional for Linux desktop use; it is not a controller dependency.

Updating/removing shared tools does not update/delete instances, databases, records,
maps, credentials, custom apps or inventories. Instance maintenance is explicit; see
`MAINTENANCE.md`. Keep instances outside the tools folder and retain known-good backups.

## Instance Files And Logs

Local process confirmations show the parent/server folder once, then paths relative
to that server folder. External paths remain absolute. Show Details retains the full
review. When present, `.env` is listed first, with the registered TOML file identified
as its base; inherited environment values can still override file settings. Existing
batch/script arguments are labelled as supplied by their launcher, not guessed.

The Dashboard lists players and spectators separately. The Console uses "instance"
for each registered server. Batch launchers need no OS service plan; connecting
already installed Windows/systemd services is an Advanced server action only.

Configuration also lists existing `GameData/Config/dedicated_cfg.txt` and
`GameData/Tracks/MatchSettings/MatchSettings.txt`, relative to the registered dedicated
executable's folder (or the controller's parent when none is registered). These are
explicit default files, not a claim that a custom launcher uses them. The full file
path is displayed. Edits preserve unrelated XML formatting and comments, use revision
checks and backups, and never restart the server or load MatchSettings automatically.
XML syntax is validated; the dedicated server validates its own settings when loading.
Repeated elements are numbered; XML attribute rules remain unchanged. Whole-file
backup restoration is also available. Controller running/stopped mode does not change
these disk-only dedicated-file operations.

Console offers controller `logfile.txt`, the newest `Logs/ConsoleLog.*.txt`, and up to
50 recent dedicated ConsoleLog/GameLog files, including `GameLog.<server_login>.txt`.
Refresh sources to discover new named files. The newest-ConsoleLog option follows
rotation automatically. Only the selected source is tailed, with bounded reads and
the existing sensitive-information display preference. Switching instances, sources
or masking settings discards stale in-flight display responses.

## Duplicate, Back Up And Select Several Instances

Right-click one instance for **Duplicate instance** or **Create backup ZIP**.
Use Ctrl/Shift to select several instances in the sidebar or Dashboard, then
right-click for Start/Stop/Restart of controllers, dedicated servers or both.
Bulk actions confirm the names first, run sequentially, and show a separate result
for each instance. One failure does not stop the remaining instances. OS service
requests still require checking runtime readiness; no forced-kill fallback is added.

Duplication copies maps, apps, custom assets and configuration, without modifying
the original. Provide a new server account, passwords, ports, database and database
user. It creates a new isolated Python environment, fresh management identity/token,
and new launchers. Logs, virtual environments, controller runtime state and old
entrypoint scripts are excluded; custom app code/state directories remain copied.
The new database has empty tables by default. Optional record copying uses a source
SQL dump and imports it only after creating the fresh database/user. Existing names
are refused. Nothing starts, no OS service is copied, and partial new resources are
retained on failure for inspection rather than deleting databases automatically.
Review copied custom API/Discord credentials and app-specific database connections.
This wizard currently supports the standard controller config.toml/.env and dedicated
dedicated_cfg.txt / MatchSettings/MatchSettings.txt launch paths; custom paths are
explicitly refused, not guessed. Source changes during copying cause a refusal.

ZIP backups include server files, credentials, logs and local state, excluding active
lock files. Choose a new ZIP outside the instance folder; existing backups are not
overwritten. SQL export is optional and uses the instance's .env/TOML credentials.
A folder-only ZIP does not contain MariaDB records. Running or unknown-state servers
require explicit acknowledgement of a possibly inconsistent snapshot; nothing is
stopped automatically. Stop both components for the safest file snapshot. SQL uses
single-transaction export: nontransactional tables and files are not one atomic
snapshot. SQL export/copy covers tables and data, not database users, triggers,
stored routines or events. MariaDB dump/client programs must be installed or chosen;
passwords are sent through private option files, not process arguments. ZIP files are
access-restricted locally but not encrypted: secure them when copying elsewhere.
Restore a backup as the same instance; use Duplicate to obtain a separate identity.

## Package Verification

This is the **PyXaseco 1.3 production release**, distributed **unsigned**. Windows
may show an unknown-publisher or SmartScreen warning. Download only from the project's
official release location and check the SHA256 hash before running the installer.
SHA256SUMS detects changed files when obtained through a trusted channel; a checksum
downloaded beside a file is not independent publisher authentication or a certificate.
No signing certificate needs to be installed, and security protections should not be
disabled to run this package. The standalone DEB is not a signed APT repository.

Windows example, from the release download folder:

```powershell
Get-FileHash .\PyXasecoSetup.exe -Algorithm SHA256
```

Compare the result with the `PyXasecoSetup.exe` entry in `SHA256SUMS`. Linux can check
the downloaded DEB with `sha256sum pyxaseco_1.3.0-1_amd64.deb` against its entry.
See `RELEASE_NOTES.md` for changes, upgrade guidance and the validation scope.
Clean-host, service-account and reboot/recovery acceptance are not certified by this build.

Build records, corresponding PyXaseco/packaging source and GPL licensing accompany the
release. Python, Qt and MariaDB retain their publisher licenses. The TMF template is
separate third-party software, not GPL source; see `TMF_TEMPLATE_NOTICE.txt`. Review
redistribution and source-offer obligations before public distribution. No MSI or RPM
distribution package is provided.

References: [Python on Windows](https://docs.python.org/3.14/using/windows.html),
[MariaDB Windows installer](https://mariadb.com/docs/server/server-management/install-and-upgrade-mariadb/installing-mariadb/binary-packages/installing-mariadb-msi-packages-on-windows),
[Debian package format](https://manpages.debian.org/trixie/dpkg-dev/deb.5.en.html).
