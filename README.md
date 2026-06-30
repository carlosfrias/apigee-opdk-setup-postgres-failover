# apigee-opdk-setup-postgres-failover — Apigee Postgres Master Failover (Controlled Switchover)

> **An Ansible role that performs a controlled Apigee Postgres master failover** — stop the old master, then `promote-standby-to-master` — so the standby becomes the new writable master with replication lineage preserved. The atomic failover primitive of the Apigee analytics datastore.

A Postgres HA switchover procedure expressed as code: knowing **why you stop the old master first**, **why `promote-standby-to-master` takes the prior-master IP as an argument**, and **how this differs from a crash failover**.

---

## Why this role is notable

- **Controlled, not crash.** The old master is stopped (not killed) so the standby's WAL stream is current before promotion — a reversible switchover, not a one-way failover.
- **Replication lineage preserved.** `promote-standby-to-master <old_master_ip>` passes the prior-master IP so the new master can be re-paired as a standby later.
- **Atomic-primitive composition.** Does one thing (failover) and is composed by the maintenance playbooks that handle the surrounding analytics datastore re-registration.

---

## What the role actually does

`tasks/main.yml` is deliberately tiny — two ordered steps:

1. **Stop the current master** — `import_role: apigee-opdk-setup-stop-component` with `component_name: apigee-postgresql`. A controlled stop, not a kill, so the standby is caught up on WAL streaming before promotion.
2. **Promote the standby** — `apigee-service apigee-postgresql promote-standby-to-master {{ pgmaster_ip }}`. Apigee's wrapper around `pg_promote()` that turns the standby into a new writable master and is given the **prior-master IP** so the promoted node knows its replication lineage (enabling a later reverse-standby setup).

---

## Capabilities — what this credentials

> Ansible is the medium. The engineering below is the evidence of the expertise applied.

- **Postgres HA switchover** — controlled (not crash) failover: stop the old master first so the standby's WAL stream is current, then promote.
- **Replication lineage awareness** — `promote-standby-to-master <old_master_ip>` passes the prior-master IP so the new master can be re-paired as a standby later — a reversible switchover.
- **Atomic-primitive composition** — the role does one thing (failover) and is composed by the maintenance playbooks that handle the surrounding analytics datastore re-registration.

---

---

## When this role is used

Composed into the broader OPDK runbooks after a master failure or planned maintenance. After this role runs, the analytics group datastore registration (`postgres-add` with the new master UUID) and any reverse-standby setup follow at the playbook layer. The role is the **atomic failover primitive**; the playbook is the choreography.

See the [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) framework and the `apigee-opdk-*` role corpus for the composition playbooks.

## Role variables

| Variable | Required | Description |
|----------|----------|-------------|
| `pgmaster_ip` | yes | The **prior** master's IP — passed to `promote-standby-to-master` as the lineage argument |
| `apigee_service` | — | Path to the `apigee-service` tool (from the keystone settings role) |

---

## Provenance

Authored and maintained by **Carlos Frias** during his tenure on Apigee Edge Private Cloud. One of the Postgres-HA roles in the `apigee-opdk-*` corpus — the analytics-datastore expertise is aggregated in the [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) framework.

Contributions welcome — see the broader framework.