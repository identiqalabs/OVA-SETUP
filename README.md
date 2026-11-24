# OVA Setup — Client License Prompt

How to prepare an Ubuntu VM that ships Identiqa NMS (Zabbix in Docker) as an
**OVA template**. On first login, the VM asks the client for their **license key**
and **instance ID**.

The full step-by-step guide is in **[ova_setup.md](ova_setup.md)**.

## What the setup does

- Creates a locked-down `client` user: console/SSH only, no GUI, no general sudo.
- Stores the license at `/home/zabbix/zabbix-docker/license/license.lic`.
  Only the `zabbix` admin user can read it.
- Gives `client` one narrow sudo rule: it can *write* the license file (via `tee`
  as `zabbix`) and do nothing else.
- Runs a `dialog` prompt at `client` login that asks for the license key and
  instance ID, saves them, and stops prompting after that.
- Boots the VM to a text console (`multi-user.target`). The admin can still start the GUI on demand.

## Guide contents

| Step | Topic |
|---|---|
| 0 | Prerequisites (`dialog`) |
| 1 | Create the `client` user |
| 2 | Create the license file and set permissions |
| 3 | Scoped sudoers rule |
| 4 | License prompt script (`/home/client/license.sh`) |
| 5 | Run the prompt on login (`.bash_profile`) |
| 6 | Boot to TTY only |
| 7 | End-to-end test |
| 8 | **Reset before exporting the OVA** (critical) |
| — | Troubleshooting table |

> **Before exporting the OVA, always do step 8.** Otherwise every VM cloned from
> the template will already look licensed.

## Related repositories

- `zabbix-docker` / `zabbix-docker1` — the Zabbix stack that reads this license file
- `license_backend` — the license API that validates the key
