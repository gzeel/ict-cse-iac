# Demo 3: Stage-Gated Pipeline

**Doel:** Studenten laten zien hoe een CI/CD-pipeline is opgebouwd uit opeenvolgende stages, en dat de `apply`-stage alleen draait bij een push naar `main`.

## Concept
- Een workflow met **4 stages** die alleen starten als de vorige slaagt (`needs:`):
  1. **Lint** — `yamllint` controleert YAML-kwaliteit
  2. **Syntax Check** — `--syntax-check` + `ansible-lint` valideren de code
  3. **Dry-Run** — `--check --diff` toont wat er zou veranderen
  4. **Apply** — voert het playbook echt uit, maar **alleen bij push naar `main`**
- Bij een **pull request** draaien alleen stages 1 t/m 3 (veilige checks).
- Het playbook installeert Apache (apache2).

## Stappenplan demo

### 1. Repository aanmaken
```bash
git clone git@github.com:<jouw-username>/demo-03-staged.git
cd demo-03-staged
cp -r /pad/naar/demo-03-stage-gated/* .
git add . && git commit -m "Start: stage-gated pipeline voor Apache"
git push -u origin main
```

### 2. Pipeline op main bekijken
- Ga naar de **Actions**-tab. Je ziet alle 4 stages draaien.

### 3. Feature branch + Pull Request demo
```bash
git checkout -b feature/update-apache-config
# Wijzig de website_inhoud variabele in install-apache.yml
git add ansible/install-apache.yml
git commit -m "Update: welkomsttekst Apache"
git push -u origin feature/update-apache-config
```
- Open een Pull Request op GitHub.
- De pipeline draait **alleen stages 1, 2 en 3** — géén apply!
- Merge de PR naar `main` → op main draaien nu alle 4 stages.

## Wat leg je uit
| Mechanisme | Waar in de workflow | Effect |
|-----------|-------------------|--------|
| `needs:` | Elke job verwijst naar de vorige | Stages lopen sequentieel; falen blokkeert de keten |
| `paths:` | Triggers alleen bij wijzigingen in `ansible/` | Voorkomt onnodige runs |
| `if: github.event_name == 'push' && github.ref == 'refs/heads/main'` | Job `apply` | Apply draait alleen op main |
| `on: pull_request` | Workflow trigger | PR's krijgen alleen safe checks |
| `--check --diff` | Dry-run stage | Toont wijzigingen zonder iets te doen |
