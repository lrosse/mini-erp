# TEMPORARY — M0-T01 Environment Discovery

This report is temporary and will be consolidated in M0-T11. It was produced on 2026-10-02 using only local read-only commands and official documentation. No installation, feature change, registry login, image download or container operation was performed in this task.

## 1. Environment summary

### System, hardware and path

- Windows 11 Home Single Language, 64-bit, build 26200.
- PowerShell 7.6.5 and Windows PowerShell 5.1 available; Bash, WSL and CMD also available.
- Intel Core i5-12500H, 12 cores and 16 threads.
- 15.69 GB total RAM; approximately 2.60 GB free during measurement.
- C: drive is NTFS on an NVMe SSD, with approximately 126 GB free during final validation.
- The path confirmed by the Product Owner is `<PROJECT_PATH>`; the folder existed, was empty, had no `.git`, contained no spaces or non-ASCII characters, and showed no known local synchronization marker. The path is short and there is no indication of an unsuitable filesystem. Effective write permission was confirmed by `git init` and the commit for this task.

### Virtualization and WSL

- WSL is installed and reports version 2.7.3, with the WSL2 kernel active.
- The only reported distribution was `docker-desktop`, stopped, running as WSL2.
- The hypervisor was detected as present, but the WMI query also returned firmware virtualization as disabled. The result is conflicting and may be a false negative.
- The Windows optional-feature query could not be completed: DISM returned error 740 and required elevation. No elevation was attempted.
- A `.wslconfig` exists, but no explicit memory, CPU or swap limits were identified among the queried parameters.
- Classification: NEEDS CONFIGURATION. The actual confirmation will be performed in M0-T03 when the Docker daemon is started.

### Containers

- Docker Desktop is installed; Docker CLI 29.4.3 and Docker Compose v5.1.3 are available.
- The `default` and `desktop-linux` contexts exist; the Linux context is selected, but the daemon was unreachable and the Docker Desktop process was not active during measurement.
- Existing image, container and volume counts could not be verified because the daemon was stopped. Project names were not listed.
- Podman and nerdctl were not detected.
- The intended backend is Linux/WSL2. Windows Home does not support Windows containers according to Docker documentation.
- Docker Desktop's documented default virtual-disk location is under the user's local profile; actual presence/use was not confirmed because the daemon was stopped.

### Tools

- Git 2.53.0; local/global identity is defined without exposing the name or e-mail.
- `core.autocrlf` and `core.eol` were not previously defined.
- GitHub CLI 2.90.0 is installed and authenticated; no credential was recorded in this document.
- Temurin/OpenJDK 17.0.18 is available on PATH; `JAVA_HOME` is not defined.
- Python 3.13.14 is available.
- SQLcl, SQL*Plus, `lsnrctl`, Oracle Client, `ORACLE_HOME` and `TNS_ADMIN` were not detected.
- VS Code and Notepad are available.
- `winget`, Chocolatey and Scoop were not detected by their version commands.

### Network and ports

- TCP ports 1521, 5500, 8080, 8181, 8443, 3000, 5432, 1433 and 9000 were free.
- Windows and installed-software listeners exist on other ports, but no conflict was found on the target ports.
- Excluded ranges observed by Windows: 5357, 8883–8886 and 50000–50059. The query was performed without elevation; only ranges returned by the system were recorded.
- No proxy was detected in WinHTTP, user proxy settings or the queried environment variables.
- TCP/443 connectivity was confirmed for `container-registry.oracle.com` and `github.com`.

## 2. Official requirements consulted

Consultation date: 2026-10-02.

### Oracle AI Database 26ai Free

- Official documentation states 1 GB minimum RAM, 2 GB recommended RAM and 10 GB minimum disk space. It also recommends at least 2 GB swap or twice the RAM, whichever is less.
- The Free edition limits processing to 2 cores, database RAM to 2 GB, user data to 12 GB and one installation per logical environment. A container counts as a logical environment.
- The official image repository documents the Free 26ai image and persistent storage at `/opt/oracle/oradata`; the listener uses port 1521 and the image also exposes the administration endpoint documented by the image.
- The official download page provides the pull command for `container-registry.oracle.com/database/free:latest`. A floating tag is not recommended for this project; it must be replaced with a fixed tag/digest in M0-T03.

Sources:

- [Oracle AI Database Free — requirements](https://docs.oracle.com/en/database/oracle/oracle-database/26/xeinl/requirements.html)
- [Oracle AI Database Free — limitations](https://docs.oracle.com/en/database/oracle/oracle-database/26/xeinw/licensing-restrictions.html)
- [Oracle AI Database — licensing information](https://docs.oracle.com/en/database/oracle/oracle-database/26/dblic/Licensing-Information.html)
- [Oracle Docker Images — Single Instance](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md)
- [Oracle Database — official downloads](https://www.oracle.com/database/technologies/oracle-database-software-downloads.html)

### Oracle registry

- Official Oracle Container Registry documentation states that licensed Oracle images require an Oracle account, acceptance of the repository terms and client authentication with `docker login` or an equivalent.
- Database Free is covered by the Oracle Free Use Terms; however, effective acceptance for the `database/free` repository was not tested because this task prohibits login and pull. It must be verified in M0-T03 before downloading.

Source: [Oracle Container Registry — pulling images](https://docs.oracle.com/en/operating-systems/oracle-linux/docker/docker-UsingDockerRegistries.html).

### APEX and ORDS

- APEX is not included with the Free edition; it must be downloaded and installed separately.
- APEX 26.1 requires ORDS 26.1.1 or later. The documentation recommends installing ORDS before APEX for new installations.
- ORDS is separate from the database: it can be obtained as a ZIP/RPM or as its own Oracle Container Registry image. The ORDS image includes GraalVM and an ORDS production distribution.
- For APEX with ORDS, the APEX ZIP must be installed, static resources made available and the PL/SQL Gateway configured. The APEX ZIP and its checksums do not belong in the public Git repository.

Sources:

- [Oracle AI Database Free — excluded components](https://docs.oracle.com/en/database/oracle/oracle-database/26/dblic/Licensing-Information.html)
- [APEX 26.1 — requirements](https://docs.oracle.com/en/database/oracle/apex/26.1/htmig/apex-installation-requirements.html)
- [ORDS 26.1 — installation and configuration](https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/26.1/ordig/installing-and-configuring-oracle-rest-data-services.html)
- [APEX — installing APEX and ORDS](https://docs.oracle.com/en/database/oracle/apex/26.1/htmig/installing-and-configuring-apex-and-ords.html)

### Docker Desktop and WSL2

- Docker documents WSL 2.1.5 or later, 8 GB system RAM and hardware virtualization enabled as prerequisites for the WSL2 backend.
- Windows Home supports Linux containers only.
- For bind-mount performance, Docker recommends keeping files mounted into Linux containers in the Linux filesystem. For the database, this task has a stricter rule: use a named Docker volume and never bind-mount a Windows folder.

Sources:

- [Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- [Docker Desktop with the WSL2 backend](https://docs.docker.com/desktop/features/wsl/)
- [Docker — WSL2 and bind-mount best practices](https://docs.docker.com/desktop/features/wsl/best-practices/)

### SQLcl

- Current SQLcl documentation states Java 17 or 21; the existing Java 17 is compatible.
- SQLcl is distributed separately as a ZIP and is not installed locally.

Source: [Oracle SQLcl — download and installation](https://docs.oracle.com/en/database/oracle/apex/26.1/aeadm/downloading-and-installing-sqlcl.html).

## 3. Phase 5 classification

| Requirement | Classification | Reason and suggested action |
|---|---|---|
| Oracle AI Database 26ai Free | NEEDS CONFIGURATION | CPU, disk and total RAM are nominally sufficient, but only 2.6 GB was free, the daemon is stopped and virtualization must be confirmed in M0-T03; measure the Docker/WSL2/Oracle stack before proceeding. |
| Oracle APEX | NEEDS INSTALLATION | APEX is separate from Free and is not installed; choose a versioned ZIP, fixed checksum and cache outside Git. |
| ORDS | NEEDS INSTALLATION | It is not installed; install a version compatible with APEX 26.1, at least 26.1.1, preferably fixed by version/digest. |
| Container runtime + Compose | NEEDS CONFIGURATION | Docker Desktop and Compose are installed, but the daemon is not active; confirm the Linux/WSL2 backend and limits in M0-T03. |
| SQLcl + compatible Java | NEEDS INSTALLATION | Java 17 is compatible, but SQLcl is absent; install a fixed version when authorized. |
| PL/SQL | NEEDS CONFIGURATION | No local Oracle installation exists; PL/SQL will run in the Oracle container through SQLcl, with SQL/PLSQL files edited locally. |
| utPLSQL | NEEDS INSTALLATION | It was not detected; install it later in the database from a fixed release, in its own task. |
| Migrations | NEEDS DECISION | Liquibase through SQLcl versus numbered SQL with a simple runner is not decided; preserve both for the M0 experiment. |
| Git and GitHub CLI | READY | Git, identity and authenticated GitHub CLI are available; apply `.gitattributes` in M0-T02 to make line endings explicit. |
| CI — GitHub Actions | READY | No local runner is required; GitHub CLI is authenticated and Java/Python are available, but activation depends on the remote repository and a future task. |
| Developer scripting language | NEEDS DECISION | PowerShell 7 and Bash are available; recommendation: PowerShell for local Windows orchestration and Bash for CI Linux compatibility, without deciding in this task. |

## 4. Risks, conflicts and open questions

- **RAM:** 2.6 GB was free during measurement, against the combined needs of Oracle Free, ORDS and the WSL2 VM. Nominal machine capacity does not guarantee operating margin; measure peak usage in M0-T03.
- **Virtualization:** the firmware flag returned a result conflicting with an active hypervisor and installed WSL2. Treat it as NEEDS CONFIGURATION and confirm by starting the daemon, without changing firmware in this task.
- **APEX:** it is not bundled with Free and must not be committed. Future setup must download a ZIP with a fixed version and checksum into a Git-ignored cache.
- **Compatibility:** APEX 26.1 requires ORDS 26.1.1 or later.
- **Data limit:** it remains open whether APEX's internal schema/objects count in practice against Free's 12 GB user-data limit; measure and document this during installation.
- **Line endings:** `core.autocrlf` was not defined; create an explicit policy in `.gitattributes` in M0-T02.
- **Persistence:** in M0-T03, database data must use a named Docker volume, never a bind mount to a Windows folder.
- **Ports:** no target port is occupied; Windows excluded ranges must be considered when choosing alternatives.
- **Path:** the confirmed path is short, local, NTFS, without spaces/non-ASCII characters and on an SSD; no known conflict exists. Windows-to-Linux bind mounts can have lower performance, as documented by Docker; this does not apply to the named database volume.
- **Registry/network:** connectivity was confirmed, but login, terms acceptance and pull were not tested due to task restrictions.

Questions affecting architecture:

1. Migrations: Liquibase through SQLcl provides a declarative tool but adds dependency and complexity; numbered SQL with a simple runner reduces dependencies but requires project conventions and control. The decision belongs to the M0 experiment.
2. APEX/ORDS: separate containers or a controlled composition are compatible with the documentation; the final choice should consider memory, upgrades and persistence without embedding binaries in the repository.
3. Oracle image: a floating tag simplifies updates, while a fixed tag/digest improves reproducibility; fixing it is recommended for the public project.

## 5. Recommendations

- In M0-T03, start Docker Desktop only when authorized, confirm virtualization through daemon operation and measure free memory before and after each service.
- Fix the Oracle image, ORDS and APEX versions/digests; record checksums and keep downloads in a Git-ignored cache.
- Use a named volume for `/opt/oracle/oradata`; do not store database data in a Windows bind mount.
- Use `.gitattributes` to make line endings deterministic.
- Recommend PowerShell 7 for local Windows automation and Bash for scripts executed in Linux CI; leave the formal decision to the architect.

## 6. Validation for M0-T01

- No software, OS feature, image or container was installed, created, downloaded or started.
- No system configuration, PATH, environment variable, registry or Docker setting was changed.
- The document was reviewed to avoid containing an OS username, hostname, e-mail, IP, serial number, token, environment-variable value, personal PATH or another project name.
- The only work artifact is this document, apart from normal Git metadata required for the commit.

## Resolved in M0-T02

- Line endings are normalized through `.gitattributes`; this repository uses local `core.autocrlf=false` and `core.eol=lf` settings.
- Repository language: English.
- Developer scripting language: PowerShell 7 (`pwsh`), as decided by the architect.
