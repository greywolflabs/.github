<div align="center">

<img src="https://avatars.githubusercontent.com/u/333492045?s=240&v=4" alt="GreyWolf Labs" width="120">

# GreyWolf Labs

**Open-source tools for teams that run GitHub Actions across many repositories.**

[Website](https://actionsmanager.io) &nbsp;·&nbsp; [Documentation](https://actionsmanager.io) &nbsp;·&nbsp; [Demo video](https://youtu.be/8TrgE9YDGXA) &nbsp;·&nbsp; [Marketplace](https://github.com/marketplace/actions/am-build-vars)

</div>

---

The same CI workflow gets copied into every repository, and the copies drift apart: one pins an old action, another skips a step, a third was hand-edited during an incident. We build tools that keep **one definition of each workflow**, deliver it everywhere it belongs, and still let each repository build the way it needs to.

## Projects

<table>
<tr>
<td width="50%" valign="top">

### [ActionsManager](https://github.com/greywolflabs/actions-manager)

A self-hosted control plane for GitHub Actions. Keep one definition per workflow file, deliver it to every repository and branch as pull requests, and see the moment a copy drifts.

[![Release](https://img.shields.io/github/v/release/greywolflabs/actions-manager?include_prereleases&sort=semver&label=release)](https://github.com/greywolflabs/actions-manager/releases)
[![License](https://img.shields.io/github/license/greywolflabs/actions-manager)](https://github.com/greywolflabs/actions-manager/blob/main/LICENSE)
[![ghcr.io](https://img.shields.io/badge/ghcr.io-actions--manager-2496ED?logo=docker&logoColor=white)](https://github.com/greywolflabs/actions-manager/pkgs/container/actions-manager)

**[Docs](https://actionsmanager.io)** · **[Quick start](#run-actionsmanager)** · **[Walkthrough](https://actionsmanager.io/getting-started/first-workflow-walkthrough)**

</td>
<td width="50%" valign="top">

### [am-build-vars](https://github.com/greywolflabs/am-build-vars)

A composite GitHub Action that reads per-repository settings from a committed `am-build-vars.yml` and exports them as environment variables, so one byte-identical workflow builds differently in every repository.

[![Release](https://img.shields.io/github/v/release/greywolflabs/am-build-vars?label=release)](https://github.com/greywolflabs/am-build-vars/releases)
[![License](https://img.shields.io/github/license/greywolflabs/am-build-vars)](https://github.com/greywolflabs/am-build-vars/blob/main/LICENSE)
[![Marketplace](https://img.shields.io/badge/Marketplace-AM%20Build%20Vars-2088FF?logo=githubactions&logoColor=white)](https://github.com/marketplace/actions/am-build-vars)

**[README](https://github.com/greywolflabs/am-build-vars#readme)** · **[Usage](#use-am-build-vars)** · **[Changelog](https://github.com/greywolflabs/am-build-vars/blob/main/CHANGELOG.md)**

</td>
</tr>
</table>

## ActionsManager

<p align="center">
  <img src="https://raw.githubusercontent.com/greywolflabs/am-build-vars/main/docs/actionsmanager-logo.png" alt="ActionsManager" width="380">
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/greywolflabs/actions-manager/main/docs/assets/readme/pr-campaign.gif" alt="Editing a workflow, then opening pull requests to three repositories in one PR campaign" width="820">
</p>

- **Projects** group repositories with the files you manage for them: workflows, custom files and CODEOWNERS.
- **PR campaigns** deliver a change to every repository and branch in a project as pull requests. Review, merge, close or roll them back from one place.
- **Drift detection** re-checks every repository and branch in the background and flags a delivered file that was edited or deleted in GitHub.
- **Reusable and caller workflows**, a YAML and GUI **workflow editor**, and **build-type detection** for Maven, Gradle, npm, .NET, Python, Go, Rust, Docker and more.
- **Secrets, variables, deploy environments and rulesets** managed across a project's repositories.

### Run ActionsManager

One self-hosted container on port 8080. Generate a secret key once and reuse it on every restart:

```bash
openssl rand -hex 32
```

```bash
docker run -d --name actions-manager -p 8080:8080 \
  -v actions-manager-data:/app/data \
  -e INSTALLATION_MODE=self-hosted \
  -e SECRET_KEY=<your_generated_key> \
  -e ALLOW_INSECURE_HTTP=true \
  ghcr.io/greywolflabs/actions-manager:latest
```

Open `http://localhost:8080` and sign in with a GitHub personal access token. `ALLOW_INSECURE_HTTP` is only for plain-HTTP local use. See the [HTTPS setup guide](https://actionsmanager.io/getting-started/https-setup) before exposing it.

> ActionsManager is a **free, self-hosted beta**. Read the [Beta Notes](https://github.com/greywolflabs/actions-manager/blob/main/BETA.md) for its limits and your responsibilities as an operator.

## am-build-vars

### Use am-build-vars

Commit one workflow, identical in every repository, with fleet-wide defaults inline:

```yaml
steps:
  - uses: actions/checkout@v7
  - uses: greywolflabs/am-build-vars@v1
    with:
      defaults: |
        node_version: "20"
        test_command: npm test

  - uses: actions/setup-node@v7
    with:
      node-version: ${{ env.node_version }}
  - run: ${{ env.test_command }}
```

A repository that needs something different commits an `am-build-vars.yml` overriding only that key:

```yaml
# am-build-vars.yml
node_version: "24"
```

- **The key is the variable name.** `node_version` becomes `$node_version` and `${{ env.node_version }}`, with no prefix and nothing to declare.
- **Shares values across workflows.** Publish a value a run computed, such as an image tag, and read it back in a different workflow file later.
- **Small and auditable.** Three short Python files, no package dependencies, and no network calls unless sharing is on.
- **Works standalone.** No dependency on ActionsManager. Runs on Linux and macOS runners.

## How they fit together

| File | Owned by | Same in every repo? |
|---|---|---|
| `.github/workflows/build.yml` | the fleet, delivered by ActionsManager | **Yes.** Drift detection keeps it that way. |
| `am-build-vars.yml` | each repository's own team | No. This is where per-repo variation lives. |

ActionsManager ships am-build-vars in its default Managed Actions catalog, so it's ready in the GUI workflow editor.

## Get involved

- **Questions and ideas:** [Discussions](https://github.com/greywolflabs/actions-manager/discussions)
- **Bugs and feature requests:** [ActionsManager issues](https://github.com/greywolflabs/actions-manager/issues) · [am-build-vars issues](https://github.com/greywolflabs/am-build-vars/issues)
- **Contributing:** fork [greywolflabs/actions-manager](https://github.com/greywolflabs/actions-manager) and open your pull request against the `staging` branch. See [CONTRIBUTING.md](https://github.com/greywolflabs/actions-manager/blob/main/CONTRIBUTING.md).
- **Security:** please don't open public issues for vulnerabilities. Follow the private reporting route in [SECURITY.md](https://github.com/greywolflabs/actions-manager/blob/main/SECURITY.md).

---

<div align="center">
<sub>ActionsManager is licensed under <a href="https://github.com/greywolflabs/actions-manager/blob/main/LICENSE">Apache 2.0</a> · am-build-vars under <a href="https://github.com/greywolflabs/am-build-vars/blob/main/LICENSE">MIT</a> · © 2025–2026 GreyWolf Labs</sub>
</div>
