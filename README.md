# apigee-opdk-setup-postgres-failover — Apigee Postgres Master Failover (Controlled Switchover)

> 🔄 **Evolution note:** The automation approach from this OPDK-era role has been consolidated into the `apigee-hybrid-workspace` Ansible collection. See the successor capability in the portfolio hub: [`carlosfrias/apigee-hybrid-workspace`](https://github.com/carlosfrias/apigee-hybrid-workspace) → `bap_coe/private_cloud/` and `bap_coe/apigee_hybrid/`. The collection README explains each role group’s business value and production context.


> **An Ansible role that performs a controlled Apigee Postgres master failover** — stop the old master, then `promote-standby-to-master` — so the standby becomes the new writable master with replication lineage preserved. The atomic failover primitive of the Apigee analytics datastore.

> [!NOTE]
> Engineering portfolio note — this project demonstrates Postgres HA controlled switchover with replication lineage preservation. See the [skills assessment →](SKILLS-ASSESSMENT.md) for the expertise applied.

A Postgres HA switchover procedure expressed as code: knowing **why you stop the old master first**, **why `promote-standby-to-master` takes the prior-master IP as an argument**, and **how this differs from a crash failover**.

---

## What the role actually does

`tasks/main.yml` is deliberately tiny — two ordered steps:

1. **Stop the current master** — `import_role: apigee-opdk-setup-stop-component` with `component_name: apigee-postgresql`. A controlled stop, not a kill, so the standby is caught up on WAL streaming before promotion.
2. **Promote the standby** — `apigee-service apigee-postgresql promote-standby-to-master {{ pgmaster_ip }}`. Apigee's wrapper around `pg_promote()` that turns the standby into a new writable master and is given the **prior-master IP** so the promoted node knows its replication lineage (enabling a later reverse-standby setup).

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