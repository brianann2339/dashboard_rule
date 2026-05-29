# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a **documentation-only** repository. It does not contain the application source code, build tooling, tests, or a package manifest. There are no build/lint/test commands to run.

It holds two parallel descriptions — one Markdown, one styled HTML — of how an external **emergency-room "war room" dashboard (戰情室)** parses its data and computes its KPIs and charts:

- `README.md` — the calculation spec in Markdown tables.
- `圖表計算方式說明.html` — the same spec rendered as a standalone styled HTML page (self-contained, inline `<style>`, no external assets or JS).

The two files describe the **same logic** and must be kept in sync. When the underlying calculation rules change, update **both** files so they do not drift.

## The system being documented

The dashboard itself lives elsewhere (a Google Apps Script project, `Code.gs`, plus Google Sheets — none of which are in this repo). Understanding its data model is the key to editing these docs correctly. Data comes from three distinct source types, each tagged with a colored label/badge in the docs:

- **爬蟲擷取 / [系統爬蟲擷取] (green, "scraped"):** Real-time patient data scraped from the hospital ER web page (`ErBedQry_ER.aspx`). Fields scraped: 狀態 (status), 檢傷級別 (triage level 1–5), 掛號時間 (registration time), 床位名稱 (bed name), 科別 (department), 動向 (disposition: OA/PA/OACU/PACU, etc.).
- **直接給定數字 / [直接給定數字] (orange, "manual / hardcoded"):** Staffing schedules hardcoded as constants in the script — `NURSING`, `SCHEDULE`, `NP_SCHEDULE`. The running time-of-day selects the on-shift counts.
- **外部試算表 / [外部歷史試算表] (purple, "external sheet"):** 28-day historical bed-wait data read from a separate Google Sheet (`ER_Data` worksheet).

## Key domain concepts (referenced throughout the docs)

- **痛苦指數 (Pain Index):** `environmental pressure (additive) − team capacity (subtractive)`, floored at 0. Each contributing factor has a specific weight (e.g. resus-room patients ×10, stay >72h ×10, attending physicians −×10). Senior attendings are an exception — subtracted from team capacity.
- **`classifyPatient()`:** Keyword-matching function that sorts each patient row into a zone (急救室/resus, 婦產科/obgyn, 兒科/peds, 留觀/obs, A 區/zoneA, B 區/zoneB) using the bed-name and department fields. A 區 is the default catch-all zone.
- **Charts** are split between the main page (`id="trend"` 病人數趨勢, `id="ratioChart"` 護病比趨勢) and pop-up windows triggered by clicking KPI tiles. Each chart entry documents its source label, the scraped/sheet/constant fields it reads, and its computation.

## Conventions when editing

- The content language is **Traditional Chinese (zh-TW)**; preserve it. `code` spans are used for identifiers (`NURSING`, `classifyPatient()`, `id="trend"`, `OACU`).
- Specific numeric weights, time-of-day thresholds, and zone keyword rules are the substance of these docs — change them only to match the actual `Code.gs` logic, and apply each change to both `README.md` and the HTML.
- The HTML uses a fixed visual vocabulary: `.src-label` (green scraped), `.src-label.manual` (orange), `.src-label.sheet` (purple), `.badge` (page-location tag), `.math-box` (formulas), `.plus`/`.minus` (pressure vs. capacity). Reuse these classes rather than inventing new styles.

## Git

Active development branch for this work: `claude/claude-md-docs-QWCOf`. Push with `git push -u origin <branch>`. Do not open a pull request unless explicitly asked.
