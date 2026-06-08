# ga-concurrency

A live demo of the GitHub Actions **concurrency groups** feature and the newer
`cancel-in-progress: false` behavior that lets runs queue instead of dropping.

---

## What this demo shows

| Setting | Behaviour |
|---|---|
| `cancel-in-progress: true` *(old default)* | One run executes, one waits — any further run **cancels** the waiting run. |
| `cancel-in-progress: false` *(new)* | One run executes, up to **100** more wait in order — none are dropped. |

Concurrency groups now allow larger queues *(released May 7, 2026)*.

---

## Workflows

### [`deploy.yml`](.github/workflows/deploy.yml)

Simulates a 30-second production deployment.  Uses:

```yaml
concurrency:
  group: deploy-production
  cancel-in-progress: false   # keep pending runs instead of cancelling them
```

Trigger it manually from the **Actions** tab (`workflow_dispatch`) and supply an
optional `version` label.

### [`trigger-demo.yml`](.github/workflows/trigger-demo.yml)

Fires several `deploy.yml` runs back-to-back (2–5, default 3) so you can watch
them queue up in the Actions UI under the shared `deploy-production` concurrency
group — **none are cancelled**.

---

## Running the demo

1. Go to **Actions → Trigger Concurrency Demo** and click **Run workflow**.
2. Leave the default of **3** runs (or pick up to 5).
3. Switch to **Actions → Deploy to Production** and refresh — you will see:
   - **Run #1** `in progress`
   - **Run #2** `queued`
   - **Run #3** `queued` … and so on up to the number you chose.
4. Each run completes in order; no run is silently dropped.

---

## Key takeaways

- Ordered deployments against a shared environment no longer drop queued runs.
- Less need for homegrown locking / serialization tooling.
- Pairs naturally with `cancel-in-progress: false` to preserve every pending run.