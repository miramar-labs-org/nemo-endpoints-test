# CLAUDE.md

## What this repo is

`nemo-endpoints-test` — systematic HTTP endpoint tester for all NeMo Microservices APIs on the Miramar platform (DGX Spark). Run the notebook to verify which services are reachable and healthy.

## Key files

| File | Purpose |
|---|---|
| `notebook.ipynb` | Endpoint tester — one cell group per service, summary table at end |
| `job_config.yaml` | NeMo job config (kept for Deploy/Undeploy workflow compatibility) |
| `scripts/submit_job.py` | Called by Deploy to NeMo workflow |
| `scripts/cancel_job.py` | Called by Undeploy from NeMo workflow |

## NeMo API access

SSH tunnel from laptop: `ssh -L 8082:localhost:8082 -L 8888:localhost:8888 spark-79b7.local`

Laptop `/etc/hosts`: `127.0.0.1 nemo.test nim.test data-store.test`

PORT in notebook defaults to `8082` (matches the DGX NeMo ingress port-forward).

## Endpoint coverage

Three hosts, ~20 endpoints total:
- `nemo.test:8082` — entity store, deployment, core API, customizer, evaluator, data designer, auditor, safe-synthesizer, intake
- `nim.test:8082` — NIM inference proxy (OpenAI-compatible); chat completions tested dynamically if a model is deployed
- `data-store.test:8082` — HuggingFace-compatible data store health check

Services not deployed return SKIP (connection error/timeout), not FAIL.

## Platform repo

[miramar-labs-org/miramar-platform-gcp](https://github.com/miramar-labs-org/miramar-platform-gcp) — trigger NeMo Deploy there before running tests.
