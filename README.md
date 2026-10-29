# Terminus Ops

This repository regroups the configuration-as-code to provision, configure,
deploy and manage the EPFL's [Terminus] server (TRMNL e-ink displays) and its
EWS room-booking companion apps. It uses [Ansible] wrapped in a convenient
[suitcase], called [`terminusible`](./terminusible).

All the "dev" code can be found in https://github.com/epfl-si/drcv-esp32
(branch `feature/terminus`).


## Prerequisites

* SSH access as `root` to `itswbhst0032.xaas.epfl.ch` (new VM).
* For the migration only: SSH access as `ubuntu` (with `sudo`) to `86.119.34.249` (old VM).
* The two secret files in `roles/terminus-vm/files/` (git-ignored):
  * `.env`: Terminus, Postgres and Valkey settings
  * `ews-api.env`: Exchange and Azure credentials

  TODO: move them to [Keybase] `/keybase/team/epfl_idevfsd/terminus/`.


## TL;DR

`./terminusible`

Setup the VM, install Docker, clone the code, copy the secrets, build and run
the containers (Caddy, Terminus, Postgres, Valkey, EWS API and frontend).

Use `-t setup`, `-t build` or `-t run` to run only one step.

Use `--check --diff` to see what would change, without changing anything.


## Migration from the old VM

`./terminusible -t migrate`

Copies the database (`pg_dump`) and the uploads volume from the old VM to the
new one, then restarts Terminus. The old VM keeps running.

⚠️ **Never run it after the Cloudflare switch**: it would overwrite the new
production data with older data.

On D-day:
1. `./terminusible -t migrate`
2. Change the origin of `terminus.fsd.epfl.ch` in Cloudflare to `128.178.218.22`
3. Check the website and the displays
4. Stop the old stack: `cd ~/drcv-esp32 && sudo docker compose stop`

Rollback: put `86.119.34.249` back in Cloudflare, then
`sudo docker compose start` on the old VM.


[Terminus]: https://github.com/usetrmnl/terminus
[Ansible]: https://www.ansible.com (Ansible is Simple IT Automation)
[suitcase]: https://github.com/epfl-si/ansible.suitcase (Install Ansible and its dependency stack into a temporary directory)
[Keybase]: https://keybase.io