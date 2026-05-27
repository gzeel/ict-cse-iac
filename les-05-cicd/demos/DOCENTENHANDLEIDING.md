# Docentenhandleiding — Demo's CI/CD (Week 5)

> Alle demo's draaien vanaf je laptop met alleen een GitHub-account.
> Elke demo staat in een eigen submap onder `demos/` en is een op zichzelf staande GitHub-repo.

---

## Overzicht demo's

| # | Demo | Duur | Kernconcept | Moeilijkheid |
|---|------|------|-------------|-------------|
| 1 | Falende pipeline | ~8 min | Linting + syntax-check in CI | ★☆☆ |
| 2 | Local Runner in actie | ~12 min | Self-hosted runner; playbook echt uitvoeren | ★★☆ |
| 3 | Stage-gated pipeline | ~8 min | Multi-stage workflow; PR vs main verschil | ★★☆ |
| 4 | Terraform pipeline | ~12 min | fmt, validate, tflint, plan, apply, destroy | ★★★ |
| 5 | Debug, Assert & Fail | ~5 min | Ansible runtime testing | ★☆☆ |

**Aanbevolen volgorde:** 5 → 1 → 3 → 2 → 4 (van simpel naar complex)

---

## Voorbereiding (vóór de les)

```bash
# Tools installeren (macOS)
brew install ansible yamllint terraform tflint docker git gh

# Controleer installaties
ansible --version && yamllint --version && terraform --version && tflint --version && docker --version
```

---

## Demo 1: De Falende Pipeline (8 min)

```bash
git clone git@github.com:<USER>/demo-01-cicd.git && cd demo-01-cicd
cp -r /Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos/demo-01-falende-pipeline/{.github,.yamllint,ansible,README.md} .

# Push kapot playbook → ROOD op Actions
git add . && git commit -m "Start: kapot playbook" && git push

# Fix → GROEN
cp ansible/playbook-fixed.yml ansible/playbook-broken.yml
git add . && git commit -m "Fix: playbook gecorrigeerd" && git push
```

**Zeg:** "Elke push wordt automatisch getest. yamllint vindt 5 fouten, syntax-check vindt de typo. Dit is je eerste verdedigingslinie."

---

## Demo 2: Local Runner in Actie (12 min)

```bash
git clone git@github.com:<USER>/demo-02-runner.git && cd demo-02-runner
cp -r /Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos/demo-02-local-runner/{.github,ansible,README.md} .
git add . && git commit -m "Start: nginx playbook" && git push

# Runner installeren (terminal 1 — laat open!)
mkdir -p ~/actions-runner && cd ~/actions-runner
curl -o actions-runner.tar.gz -L https://github.com/actions/runner/releases/download/v2.322.0/actions-runner-linux-x64-2.322.0.tar.gz
tar xzf actions-runner.tar.gz
./config.sh --url https://github.com/<USER>/demo-02-runner --token <TOKEN>
./run.sh

# Trigger pipeline (terminal 2)
cd ~/demo-02-runner
# Wijzig welkom_tekst in ansible/install-nginx.yml
git add ansible/install-nginx.yml && git commit -m "Update" && git push
```

**Zeg:** "De runner op mijn laptop ontvangt de job van GitHub en voert 'm uit. Resultaat: nginx draait."

---

## Demo 3: Stage-Gated Pipeline (8 min)

```bash
git clone git@github.com:<USER>/demo-03-staged.git && cd demo-03-staged
cp -r /Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos/demo-03-stage-gated/{.github,ansible,README.md} .
git add . && git commit -m "Start: stage-gated pipeline" && git push -u origin main

# Feature branch → PR → merge
git checkout -b feature/update-tekst
# Wijzig website_inhoud in ansible/install-apache.yml
git add ansible/install-apache.yml && git commit -m "Update tekst" && git push -u origin feature/update-tekst
# Open PR → alleen stages 1-3 (géén apply). Merge → alle 4 stages.
```

**Zeg:** "Bij een PR: alleen lint, syntax en dry-run. Na merge naar main: ook apply. Stage-gating voorkomt dat ongeteste code in productie komt."

---

## Demo 4: Terraform Pipeline (12 min)

```bash
git clone git@github.com:<USER>/demo-04-terraform.git && cd demo-04-terraform
cp -r /Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos/demo-04-terraform-pipeline/{.github,terraform,README.md} .
git add . && git commit -m "Start: Terraform Docker pipeline" && git push -u origin main

# Path-filter demo: README triggert niets
echo "## Extra" >> README.md
git add README.md && git commit -m "docs" && git push  # geen Terraform run!

# Terraform file triggert wel
sed -i '' 's/8080/9090/' terraform/main.tf
git add terraform/main.tf && git commit -m "feat: poort 9090" && git push  # pipeline loopt!

# Manuele destroy
# Actions → "Terraform — Destroy (Handmatig)" → Run workflow → vul "DESTROY" in
```

**Zeg:** "Path-filters besparen runner-minuten. tflint checkt best practices. Manuele destroy voorkomt per ongeluk verwijderen."

---

## Demo 5: Debug, Assert & Fail (5 min)

```bash
cd /Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos/demo-05-debug-assert/ansible

# Succesvol
ansible-playbook debug-assert-demo.yml

# Fail activeren: zet demo_fail_trigger: true
ansible-playbook debug-assert-demo.yml

# Assert activeren: zet demo_assert_trigger: true, demo_fail_trigger: false
ansible-playbook debug-assert-demo.yml

# Dry-run
ansible-playbook --check debug-assert-demo.yml
```

**Zeg:** "Test je infrastructuurcode zoals applicatiecode. Debug, assert en fail zijn je gereedschap."

---

## Alle paden

```bash
DEMOS=/Users/gerrit/Code/ict-cse-iac/les-05-cicd/demos

$DEMOS/demo-01-falende-pipeline/
$DEMOS/demo-02-local-runner/
$DEMOS/demo-03-stage-gated/
$DEMOS/demo-04-terraform-pipeline/
$DEMOS/demo-05-debug-assert/
```
