# Skills Assessment — apigee-opdk-setup-postgres-failover

> **Skill domain:** Postgres HA — controlled master switchover for the Apigee analytics datastore (OPDK). Part of the broader Apigee platform-operations portfolio; see the [`bap_coe` portfolio hub →](https://github.com/carlosfrias/apigee-hybrid-workspace/blob/master/SKILLS-ASSESSMENT.md) for the cloud-native (Hybrid/K8s) counterpart and the full corpus.

---

## Why this role is notable

- **Controlled, not crash.** The old master is stopped (not killed) so the standby's WAL stream is current before promotion — a reversible switchover, not a one-way failover.
- **Replication lineage preserved.** `promote-standby-to-master <old_master_ip>` passes the prior-master IP so the new master can be re-paired as a standby later.
- **Atomic-primitive composition.** Does one thing (failover) and is composed by the maintenance playbooks that handle the surrounding analytics datastore re-registration.

---

## Expertise demonstrated

> Ansible is the medium. The engineering evidence lives in the [project README →](README.md). What follows is the skills assessment for the business reader.

- **Postgres HA switchover** — controlled (not crash) failover: stop the old master first so the standby's WAL stream is current, then promote. The distinction between a reversible switchover and a one-way failover is the core judgment.
- **Replication lineage awareness** — `promote-standby-to-master <old_master_ip>` passes the prior-master IP so the new master can be re-paired as a standby later — a reversible switchover, not a throwaway promotion.
- **Atomic-primitive composition** — the role does one thing (failover) and is composed by the maintenance playbooks that handle the surrounding analytics datastore re-registration. Separation of the primitive from the choreography.

---

## How this shows the expertise

This role is a Postgres HA switchover procedure expressed as code. The expertise is not "running `pg_promote()`" — it is knowing **why you stop the old master first** (so the standby's WAL stream is current before promotion — a reversible switchover, not a crash failover), and **why `promote-standby-to-master` takes the prior-master IP as an argument** (so the promoted node knows its replication lineage and can be re-paired as a standby later).

The clearest single signal: the discipline of a *controlled* stop before promotion. A crash failover is simpler; a controlled switchover preserves reversibility. That is the HA judgment — with Ansible as the medium, and the role kept deliberately tiny (two steps) because the surrounding re-registration choreography belongs at the playbook layer.

---

## Related expertise

| Skill | Repository | Assessment |
|-------|-----------|-----------|
| Rolling upgrade / DR / traffic fencing | [`apigee-opdk-playbook-maintenance-opdk-upgrade`](https://github.com/carlosfrias/apigee-opdk-playbook-maintenance-opdk-upgrade) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-opdk-playbook-maintenance-opdk-upgrade/blob/master/SKILLS-ASSESSMENT.md) ✅ |
| Cassandra cluster rebuild | [`apigee-opdk-cassandra-rebuild`](https://github.com/carlosfrias/apigee-opdk-cassandra-rebuild) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-opdk-cassandra-rebuild/blob/master/SKILLS-ASSESSMENT.md) ✅ |
| OPDK framework (flagship monorepo) | [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-edge-opdk/blob/master/SKILLS-ASSESSMENT.md) *(pending retrofit)* |
| Cloud-native (Hybrid/K8s) counterpart | [`apigee-hybrid-workspace`](https://github.com/carlosfrias/apigee-hybrid-workspace) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-hybrid-workspace/blob/master/SKILLS-ASSESSMENT.md) ✅ portfolio hub |

---

## Provenance

Authored and maintained by **Carlos Frias** during his tenure on Apigee Edge Private Cloud. This skills assessment is the companion to the engineering [README →](README.md). For the full engineering detail — the two-step procedure, variables, and composition — see the project README.

## License

See [LICENSE](./LICENSE).