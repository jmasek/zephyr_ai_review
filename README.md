# Zephyr AI Review

OpenCode configuration for comprehensive Spotflow Zephyr pull request reviews.

This repository contains development tooling and is not required to build or use the Spotflow Device SDK.

## Usage

Enable the optional west project from the workspace:

```powershell
west config manifest.group-filter +spotflow-ai-review
west update zephyr-ai-review
```

Launch OpenCode from the checked-out configuration repository:

```powershell
Set-Location opencode-config
opencode
```

Run the review command with a Spotflow Device SDK pull request number or URL:

```text
/spotflow-pr-review <pull-request>
```
