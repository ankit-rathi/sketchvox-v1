# GitHub + DeepSeek setup

## Repository
Create a **private** GitHub repository and upload the contents of this directory. Do not upload `.env`, API keys, credentials, generated caches or large rendered outputs.

Recommended first commit:

```powershell
git init
git add .
git commit -m "SketchVox 6.2.1 candidate"
git branch -M main
git remote add origin <YOUR_PRIVATE_REPO_URL>
git push -u origin main
```

## DeepSeek start point
Give DeepSeek these files first:

1. `CONTEXT_HANDOFF.md` — current project state and known issues
2. `AI_CONTRIBUTING.md` — DeepSeek's role and working rules
3. `DECISIONS.md` — architectural invariants
4. `ROADMAP.md` — product/engineering direction
5. `VALIDATION_REPORT_6.2.1.md` — latest evidence

For implementation tasks, provide an explicit specification and acceptance tests. DeepSeek should implement and report a handoff; it should not silently redesign the architecture.

## ChatGPT handoff
For a new ChatGPT account/session, paste `CONTEXT_HANDOFF.md` first. Add the relevant implementation brief or DeepSeek handoff. Do not paste the whole repository into chat unless the task requires it.

## Branch discipline
Use feature branches for substantive work:

```text
main
  └── feature/<short-name>
```

DeepSeek works on the feature branch. The project owner decides when to merge after tests and generated-artifact review.
