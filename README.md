# IPBES Technical Guideline Template

This repository is the starting point for new IPBES Technical Guidelines (TGs). It ships a Quarto template (`TG.qmd`), helper scripts, and a GitHub Actions workflow that renders, packages, and publishes the finished guideline to GitHub Pages.

## Repository layout
- `TG.qmd` – Authoring template that holds the complete guideline content, structured guidance, and Quarto metadata.
- `scripts/` – Automation helpers invoked locally or in CI:
  - `detect_r_packages.sh` – Scans `.qmd` files for R usage and captures the packages needed for evaluation.
  - `export_frontmatter.sh` – Extracts the YAML front matter from a `.qmd` file into `metadata_qmd.yaml` for downstream reuse.
- `.github/workflows/quarto-gh-pages.yml` – CI pipeline that keeps the published site in sync with `TG.qmd`.
- `LICENSE` – Default Creative Commons licence applied to the template content.

## Authoring flow
1. Create a new repository from this template and clone it locally.
2. Work on the `dev` branch (recommended) and author the TG in `TG.qmd` following the in-file checklist.
3. Run `quarto render TG.qmd --to html` to preview the guideline. Use the helper scripts if needed:
   - `bash scripts/detect_r_packages.sh TG.qmd`
   - `bash scripts/export_frontmatter.sh TG.qmd`
4. When ready, merge changes into `main`. The GitHub workflow (described below) will rebuild and publish the site to `gh-pages`.

## GitHub workflow
- **Triggers** – Runs on pushes to `main` or `master`, and can be started manually via the `workflow_dispatch` button.
- **Dependency discovery** – Executes `scripts/detect_r_packages.sh TG.qmd` to determine whether R should be set up and which packages to install. When no R usage is found it skips the R toolchain entirely.
- **Front matter export** – Calls `scripts/export_frontmatter.sh TG.qmd` to keep an up-to-date `metadata_qmd.yaml` alongside the HTML output.
- **Rendering** – Installs Quarto (and, if needed, R plus the detected packages) before rendering `TG.qmd` to `index.html`.
- **Publishing** – Copies `index.html`, `metadata_qmd.yaml`, and any generated `R.pkgs` manifest into a `public/` folder and pushes it to the `gh-pages` branch using `peaceiris/actions-gh-pages@v3` (Jekyll disabled).

## Local development tips
- Keep the YAML front matter accurate; the directory generator reads `metadata_qmd.yaml` to build the central TG catalogue.
- Set `execute: eval: true` only when code must run during rendering. Otherwise leave evaluation disabled to speed up CI and avoid undeclared dependencies.
- Refer to supporting files (figures, data) using relative paths so the render works both locally and in CI.
- Do not commit the rendered `index.html`. The workflow recreates it automatically for GitHub Pages.

## Publishing checklist
- YAML metadata (title, subtitle, authors, DOI, categories) is complete and accurate.
- Abstract and keywords reflect the final content. As the abstract is used in the directory, it should nod be to long (ideally max 500 words).
- Any required R packages were detected (`R.pkgs` present when `execute: eval: true`).
- `quarto render TG.qmd --to html` succeeds locally.
- Changes merged into `main`; workflow run completed successfully; `gh-pages` serves the up-to-date HTML.

For additional instructions and detailed authoring advice, see the guidance embedded directly within `TG.qmd`.
