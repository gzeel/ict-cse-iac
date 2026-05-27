# Demo 1: De Falende Pipeline

**Doel:** Studenten laten zien hoe een CI/CD-pipeline fouten in Ansible-code vroegtijdig detecteert.

## Concept
- Een opzettelijk kapot Ansible playbook (`playbook-broken.yml`) bevat:
  - Geen `---` document-start
  - `truthy` waarde `no` i.p.v. `false`
  - Trailing spaces op regel 9
  - Te weinig spaties voor `# comment`
  - Verkeerde indentatie (7 i.p.v. 8 spaties) op regel 12
  - Niet-bestaande module `apache2`
- De workflow draait `yamllint` + `ansible-playbook --syntax-check` en faalt.
- Daarna toon je de gefixte versie (`playbook-fixed.yml`) en de pipeline slaagt.

## Stappenplan demo

### 1. Repository aanmaken en vullen
```bash
# Maak een nieuwe GitHub-repo aan (bijv. 'ansible-cicd-demo')
# Clone de repo lokaal:
git clone git@github.com:<jouw-username>/ansible-cicd-demo.git
cd ansible-cicd-demo

# Kopieer de inhoud van deze demo-map erin:
cp -r /pad/naar/demo-01-falende-pipeline/* .
git add .
git commit -m "Start: kapot playbook"
git push
```

### 2. Pipeline zien falen
- Ga op GitHub naar de **Actions**-tab.
- Je ziet een rode (gefaalde) workflow.
- Klik erop en toon de foutmeldingen van `yamllint` en `--syntax-check`.

### 3. Het playbook fixen
```bash
cp ansible/playbook-fixed.yml ansible/playbook-broken.yml
git add .
git commit -m "Fix: playbook gecorrigeerd"
git push
```

### 4. Pipeline zien slagen
- Ga opnieuw naar de **Actions**-tab.
- De workflow is nu groen!

## Wat leg je uit
| Fout | Oorzaak | Tool die het vindt |
|------|---------|-------------------|
| Missing `---` | Geen document-start | `yamllint` |
| `no` als boolean | Ansible wil `false` | `yamllint` (met truthy-check) |
| Trailing spaces | Spaties einde regel | `yamllint` |
| Te weinig spaties comment | `#comment` i.p.v. `  # comment` | `yamllint` |
| Foute indentatie | 7 spaties i.p.v. 8 | `yamllint` |
| Module `apache2` bestaat niet | Typo in module-naam | `ansible-playbook --syntax-check` |

## Benodigdheden
- GitHub account
- Git lokaal geïnstalleerd
- (Opt.) Ansible lokaal om playbook handmatig te testen
