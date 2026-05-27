# Demo 4: Terraform CI/CD Pipeline

**Doel:** Studenten laten zien hoe een Terraform-pipeline werkt — met `fmt`, `validate`, `tflint` (best practices), `plan`, `apply` (alleen op main), en handmatige `destroy`.

## Concept
- Terraform-manifest dat een **nginx Docker-container** start (geen cloud-account nodig!).
- Pipeline met 5 stages die alleen draaien bij wijzigingen in `terraform/`:
  1. **fmt** — `terraform fmt -check` (code-stijl)
  2. **validate** — `terraform validate` (syntax & config)
  3. **tflint** — controleert best practices (naming, deprecated syntax, etc.)
  4. **plan** — `terraform plan` (laat zien wat er zou veranderen)
  5. **apply** — voert echt uit, **alleen op push naar main**
- Aparte **handmatig te starten** `destroy`-workflow die alle infrastructuur opruimt.

## Stappenplan demo

### 1. Repository aanmaken
```bash
git clone git@github.com:<jouw-username>/demo-04-terraform.git
cd demo-04-terraform
cp -r /pad/naar/demo-04-terraform-pipeline/* .
git add . && git commit -m "Start: Terraform Docker pipeline"
git push -u origin main
```

### 2. Pipeline op main bekijken
- Ga naar **Actions** → je ziet alle 5 stages (fmt → validate → tflint → plan → apply).

### 3. Path-filter demo: README wijzigen triggert géén pipeline
```bash
echo "## Extra" >> README.md
git add README.md && git commit -m "docs: update"
git push
```
- Geen Terraform-run gestart — `paths:`-filter werkt.

### 4. Path-filter demo: Terraform-file triggert wél pipeline
```bash
# Wijzig poort in terraform/main.tf (8080 → 9090)
sed -i '' 's/8080/9090/' terraform/main.tf   # macOS
git add terraform/main.tf && git commit -m "feat: poort naar 9090"
git push
```
- Pipeline draait nu wel.

### 5. Pull Request demo (géén apply)
```bash
git checkout -b feature/update-label
# Wijzig label in main.tf
git add terraform/main.tf && git commit -m "Update label"
git push -u origin feature/update-label
```
- Open PR → alleen fmt, validate, tflint, plan — géén apply.

### 6. Handmatige destroy
- Ga naar **Actions** → "Terraform — Destroy (Handmatig)" → Run workflow.
- Vul `DESTROY` in als confirm.

## Wat leg je uit
| Mechanisme | Waar | Effect |
|-----------|------|--------|
| `paths: ['terraform/**']` | Workflow trigger | Pipeline draait alleen bij Terraform-wijzigingen |
| `terraform fmt -check` | Stage `fmt` | Faalt als code niet netjes geformatteerd is |
| `terraform validate` | Stage `validate` | Controleert of de configuratie geldig is |
| `tflint` | Stage `tflint` | Controleert best practices |
| `needs:` | Elke stage | Sequential execution |
| `if: github.event_name == 'push' && github.ref == 'refs/heads/main'` | Job `apply` | Apply alleen op main |
| `workflow_dispatch` | Destroy workflow | Alleen handmatig te starten |
| `inputs.confirm` | Destroy workflow | Extra bevestiging vóór destroy |
