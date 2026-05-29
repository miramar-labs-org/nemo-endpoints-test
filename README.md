# nemo-endpoints-test

[![Open in JupyterLab](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/open-jupyterlab.yaml/badge.svg)](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/open-jupyterlab.yaml)
[![Deploy to NeMo](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/deploy-nemo.yaml/badge.svg)](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/deploy-nemo.yaml)
[![Undeploy from NeMo](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/undeploy-nemo.yaml/badge.svg)](https://github.com/miramar-labs-org/nemo-endpoints-test/actions/workflows/undeploy-nemo.yaml)

Systematic HTTP endpoint tester for all NeMo Microservices APIs on the Miramar platform.

**Type**: NeMo Microservices

## Prerequisites

NeMo running on DGX — trigger **NeMo Deploy** in [miramar-platform-gcp](https://github.com/miramar-labs-org/miramar-platform-gcp) first.

## Workflows

| Workflow | Input | Effect |
|---|---|---|
| **Open in JupyterLab** | — | Clone/pull repo to DGX `~/git-miramar-labs-org/projects/`, print direct URL |
| **Deploy to NeMo** | `job_name` (optional) | Submit a training job from `job_config.yaml` |
| **Undeploy from NeMo** | `job_name` | Cancel a training job |

## Running the endpoint tests

### 1. Open in JupyterLab

Trigger **Open in JupyterLab** from the Actions tab. It clones/pulls this repo to
`~/git-miramar-labs-org/projects/nemo-endpoints-test` on the DGX and prints a direct link.

Then open: [http://localhost:8888/lab/tree/git-miramar-labs-org/projects/nemo-endpoints-test](http://localhost:8888/lab/tree/git-miramar-labs-org/projects/nemo-endpoints-test)

### 2. Set up access

SSH tunnel from your laptop:

```sh
ssh -L 8082:localhost:8082 -L 8888:localhost:8888 <user>@spark-79b7.local
```

Add to your laptop's `/etc/hosts` (Windows: `C:\Windows\System32\drivers\etc\hosts`, WSL2: `/etc/hosts`):

```
127.0.0.1 nemo.test nim.test data-store.test
```

### 3. Run the notebook

Open `notebook.ipynb` and run all cells. Each section tests a service group. The final cell prints a summary table with endpoint, HTTP status, and PASS/FAIL/SKIP for each.

- **PASS** — HTTP 2xx
- **FAIL** — HTTP 4xx/5xx (service reachable but returned an error)
- **SKIP** — connection refused or timeout (service not deployed or not reachable)

## Endpoints tested

| Host | Path | Service |
|---|---|---|
| `nemo.test:8082` | `/v1/namespaces`, `/v1/projects`, `/v1/datasets`, `/v1/repos`, `/v1/models` | nemo-entity-store |
| `nemo.test:8082` | `/v1/deployment/model-deployments`, `/v2/inference/gateway`, `/v2/inference`, `/v2/models` | nemo-deployment-management / nemo-core-api |
| `nemo.test:8082` | `/v1/jobs` | nemo-core-api |
| `nemo.test:8082` | `/v1/customization/jobs`, `/v1/customization/models` | nemo-customizer |
| `nemo.test:8082` | `/v1/evaluation/jobs`, `/v1/evaluation/targets`, `/v2/evaluation/jobs` | nemo-evaluator |
| `nemo.test:8082` | `/v1/data-designer/jobs` | nemo-data-designer |
| `nemo.test:8082` | `/v1beta1/audit`, `/v1beta1/safe-synthesizer`, `/v1/intake` | nemo-auditor / nemo-safe-synthesizer / nemo-intake |
| `data-store.test:8082` | `/v1/health` | nemo-data-store |
| `nim.test:8082` | `/v1/models`, `/v1/chat/completions` (dynamic), `/v1/completions`, `/v1/embeddings` | nemo-nim-proxy |

## Project structure

```
notebook.ipynb       ← Endpoint tester — run all cells to test all APIs
job_config.yaml      ← NeMo training job config (for Deploy/Undeploy workflows)
scripts/
  submit_job.py        ← Called by Deploy to NeMo workflow
  cancel_job.py        ← Called by Undeploy from NeMo workflow
```
