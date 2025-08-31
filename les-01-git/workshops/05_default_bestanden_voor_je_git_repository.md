---
Default bestanden voor je Git repository
---

> [!warning] Stuk overslaan?
> Ben je al bekend met README, .gitignore en CODEOWNERS? Sla dan gerust de eerste opdrachten over en ga door bij **Opdracht 5: .github/ met templates & CI**.

> [!info] Doel van deze workshop
> Na deze workshop staat je repository **standaard netjes**: goede documentatie, code owners, ignore-regels, linting/formatting, GitHub-templates en basis-CI.

---
## Opdracht 1: Een sterke README.md
> Je README is het visitekaartje van je project. We maken een duidelijke, herbruikbare structuur.

**Stap 1:** Maak in de root van je repo een bestand `README.md`.

**Stap 2:** Plak de volgende basis-template en vul de placeholders in:

```md
# <Projectnaam>

Korte beschrijving (1–2 zinnen) van wat dit project doet en voor wie het bedoeld is.

## Inhoudsopgave
- Installatie
- Gebruik
- Ontwikkelen
- Testen
- Configuratie
- Roadmap
- Bijdragen (Contributing)
- Licentie

## Installatie
<stappen om lokaal te draaien / pakket te installeren>

## Gebruik
<voorbeeldcommando's, screenshots of korte demo>

## Ontwikkelen
- Vereisten: <Node/Java/.NET/etc.>
- Scripts: `npm run dev`, `make`, ...
- Richtlijnen: code style, branching (bijv. GitFlow), commitconventies, etc.

## Testen
- Hoe tests draaien: `npm test` / `pytest` / `dotnet test` …
- (Optioneel) Coverage, rapportage, testdata.

## Configuratie
- Omgevingsvariabelen ( `.env.example` )
- Secrets/vertrouwelijke info **nooit committen**.

## Roadmap
- [ ] Feature A
- [ ] Feature B

## Bijdragen
Zie [`CONTRIBUTING.md`](CONTRIBUTING.md) voor richtlijnen.

## Licentie
Dit project is gelicentieerd onder <LICENTIE>. Zie [`LICENSE`](LICENSE).
```

> [!success] Tip:
> Voeg bovenaan badges toe (build status, coverage, versie). Dit kan later met GitHub Actions.

---
## Opdracht 2: LICENSE kiezen
> Een duidelijke licentie voorkomt juridische onduidelijkheid.

**Stap 1:** Maak bestand `LICENSE` in de root.

**Stap 2:** Kies een licentie (meest gebruikt: **MIT**, **Apache-2.0**, **GPL-3.0**). Plak de standaardtekst van de gekozen licentie in `LICENSE`.

**Stap 3:** Verwijs ernaar in je `README.md` (zie template hierboven).

> [!warning] Let op!
> Zonder licentie is je code juridisch **niet** vrij te hergebruiken.

---
## Opdracht 3: .gitignore op maat
> Voorkom dat build-artifacts, IDE-folders en secrets in Git belanden.

**Stap 1:** Maak `.gitignore` in de root. Gebruik een basis die past bij je stack.

**Voorbeeld (Node + algemene regels):**
```gitignore
# Node / npm
node_modules/
npm-debug.log*
yarn-error.log*

# Build output
/dist/
/build/

# IDE/OS
.vscode/
.idea/
.DS_Store
Thumbs.db

# Env
.env
.env.local
```

> [!info] Tip:
> Voeg waar nodig taal-/framework-specifieke secties toe (Python, Java, .NET, Docker, etc.).

---
## Opdracht 4: CODEOWNERS instellen
> Met `CODEOWNERS` wijs je automatisch reviewers en eigenaren toe per pad.

**Stap 1:** Maak het bestand `.github/CODEOWNERS` (of in `CODEOWNERS` in de root).

**Stap 2:** Voeg regels toe per map/bestand:
```txt
# Eigenaar voor alles
*           @org/team-backend

# Specifieke paden
/docs/      @org/tech-writers
/frontend/  @gebruikersnaam
infra/**    @org/devops-team
```

**Stap 3:** Zet branch protection aan (in GitHub Settings) zodat reviews van owners vereist zijn (optioneel, maar aanbevolen).

> [!success] Resultaat:
> Nieuwe PR’s krijgen automatisch de juiste reviewers.

---
## Opdracht 5: `.github/` met templates & CI
> Maak bijdragen voorspelbaar met issue-/PR-templates en basis-automatisering.

**Stap 1:** Maak de map `.github/` met submappen:
```
.github/
  ISSUE_TEMPLATE/
  workflows/
```

**Stap 2:** Voeg een _issue form_ (YAML) toe, bv. `ISSUE_TEMPLATE/bug_report.yml`:
```yml
name: Bug report
description: Meld een fout
title: "[BUG] <korte titel>"
labels: [bug]
body:
  - type: textarea
    id: beschrijving
    attributes:
      label: Beschrijving
      placeholder: Wat ging er mis?
    validations:
      required: true
  - type: textarea
    id: stappen
    attributes:
      label: Stappen om te reproduceren
      value: |
        1.
        2.
        3.
  - type: input
    id: versie
    attributes:
      label: Versie / commit SHA
```

**Stap 3:** Voeg een Pull Request template toe: `.github/PULL_REQUEST_TEMPLATE.md`
```md
## Samenvatting
<Wat verandert er en waarom?>

## Checklist
- [ ] Tests ok / toegevoegd
- [ ] Documentatie bijgewerkt
- [ ] Geen secrets of ongewenste bestanden

## Gerelateerd
Fixes #<issue-nummer>
```

**Stap 4:** Basis CI workflow: `.github/workflows/ci.yml`
```yml
name: CI
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm test --if-present
      - run: npm run build --if-present
```

> [!info] Variatie:
> Gebruik voor andere stacks `setup-python`, `setup-java`, `setup-dotnet` etc.

---
## Opdracht 6: Formatter & linting (.prettierrc, .yamllint, .editorconfig)
> Consistente code en config-bestanden.

**Stap 1:** Voeg `prettier` toe (voor JS/TS/Markdown/JSON):

- `package.json` scripts (indien Node):
```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  },
  "devDependencies": {
    "prettier": "^3.0.0"
  }
}
```

- Config: `.prettierrc`
```json
{
  "printWidth": 100,
  "singleQuote": true,
  "trailingComma": "all"
}
```

**Stap 2:** YAML-lint: `.yamllint` (of `.yamllint.yml` in root)
```yaml
extends: default
rules:
  line-length:
    max: 120
    level: warning
  truthy:
    level: error
```

**Stap 3:** Editorconfig: `.editorconfig`
```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 2
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false
```

**Stap 4:** Voeg in CI een format-check en yamllint toe (Node-variant):
```yml
- run: npx prettier --check .
- uses: ibiqlik/action-yamllint@v3
  with:
    config_file: ./.yamllint
```

---
## Opdracht 7: .gitattributes (line-endings, linguist)
> Beheer LF/CRLF, diff en talen-detectie.

**Stap 1:** Maak `.gitattributes` in de root:
```gitattributes
# Forceer LF in repo
* text=auto eol=lf

# Binaries (geen diff)
*.png binary
*.jpg binary
*.pdf binary

# (Optioneel) GitHub Linguist overrides
docs/** linguist-documentation
```

> [!warning] Windows?
> Zet in je Git-config `core.autocrlf=input` om CRLF-ruis te voorkomen.

---
## Opdracht 8: CONTRIBUTING, SECURITY & SUPPORT
> Maak bijdragen en security-meldingen duidelijk en veilig.

**Stap 1:** `CONTRIBUTING.md`
```md
# Bijdragen
- Volg Conventional Commits (zie hieronder)
- Maak een issue aan voor grote veranderingen
- Schrijf tests waar passend
```

**Stap 2:** `SECURITY.md`
```md
# Security policy
- Meld kwetsbaarheden **privé** via <contactmethode>
- Geef versies/branches aan waarop dit geldt
- Verwacht respons binnen <x> werkdagen
```

**Stap 3:** `SUPPORT.md` (optioneel)
```md
# Support
- Hoe en waar hulp te krijgen
- SLA’s / responstijden (indien van toepassing)
```

---
## Opdracht 9: CHANGELOG en commitconventies
> Houd veranderingen bij en automatiseer versies.

**Stap 1:** Voeg `CHANGELOG.md` toe en volg _Keep a Changelog_-stijl.

**Stap 2:** Gebruik **Conventional Commits**:
```
feat: nieuwe feature
fix: bugfix
docs: documentatie
chore: onderhoud
refactor: herstructurering
perf: performance
test: tests
```

**Stap 3 (optioneel):** Commitlint configureren
- `package.json`:
```json
{
  "devDependencies": {
    "@commitlint/cli": "^19.0.0",
    "@commitlint/config-conventional": "^19.0.0",
    "husky": "^9.0.0"
  }
}
```
- `commitlint.config.cjs`:
```js
module.exports = { extends: ['@commitlint/config-conventional'] };
```
- Husky hook:
```bash
npx husky init
# in .husky/commit-msg
npx --no commitlint --edit $1
```

---
## Opdracht 10: Dependency updates (Dependabot/Renovate)
> Blijf veilig en up-to-date.

**Stap 1:** Dependabot: `.github/dependabot.yml`
```yml
version: 2
updates:
  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: weekly
    open-pull-requests-limit: 5
```

**Stap 2 (alternatief):** Renovate ( `renovate.json` in root )
```json
{
  "extends": ["config:base"],
  "labels": ["dependencies"],
  "rangeStrategy": "bump",
  "schedule": ["before 8am on monday"]
}
```

---
## Opdracht 11: Bonus – Repository verzorging
> Kleine extra’s die veel verschil maken.

- `CODE_OF_CONDUCT.md` (bijv. Contributor Covenant)
- `README`-badges (build, coverage, licentie)
- `docs/` map voor handleidingen
- Branch protection rules (reviews vereist, status checks verplicht)
- `RELEASE.md` met release-stappen

---
## Eindchecklijst
- [ ] `README.md` met alle secties
- [ ] `LICENSE` toegevoegd en genoemd in README
- [ ] `.gitignore` passend bij stack
- [ ] `.github/` met issues/PR-templates en `workflows/ci.yml`
- [ ] `CODEOWNERS` actief
- [ ] Formatters/lints: `.prettierrc`, `.yamllint`, `.editorconfig`
- [ ] `.gitattributes` voor line-endings/binaries
- [ ] `CONTRIBUTING.md`, `SECURITY.md` (en evt. `SUPPORT.md`)
- [ ] `CHANGELOG.md` + (optioneel) commitlint
- [ ] Dependabot/Renovate ingesteld

> [!success] Klaar!
> Je repo is nu professioneel ingericht en klaar voor samenwerking.

