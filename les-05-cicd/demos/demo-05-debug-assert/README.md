# Demo 5: Debug, Assert & Fail in Ansible

**Doel:** Studenten tonen hoe je Ansible-playbooks kunt testen en valideren met de ingebouwde modules `debug`, `assert` en `fail`.

## Concept
- Een playbook dat demo's geeft van drie testmodules:
  1. **`debug`** — print variabelen, command-uitvoer, en conditionele berichten.
  2. **`assert`** — valideert condities; stopt het playbook als een conditie faalt.
  3. **`fail`** — stopt het playbook op basis van een `when`-conditie.
- Zet `demo_fail_trigger` of `demo_assert_trigger` op `true` om het faalgedrag te activeren.

## Stappenplan demo

### 1. Playbook draaien (succesvol)
```bash
cd ansible/
ansible-playbook debug-assert-demo.yml
```

### 2. Fail-module activeren
```yaml
  vars:
    demo_fail_trigger: true
```
```bash
ansible-playbook debug-assert-demo.yml
# Playbook stopt bij taak "3A — Fail-module"
```

### 3. Assert-module activeren
```yaml
  vars:
    demo_fail_trigger: false
    demo_assert_trigger: true
```
```bash
ansible-playbook debug-assert-demo.yml
# Playbook stopt bij taak "2B — Assert met meerdere condities"
```

### 4. Dry-run modus
```bash
ansible-playbook --check debug-assert-demo.yml
```

## Wat leg je uit
| Module | Gedrag bij slagen | Gedrag bij falen | Gebruik |
|--------|-----------------|-----------------|---------|
| `debug` | Print bericht of variabele | NVT (faalt nooit) | Inspecteren van state tijdens ontwikkeling |
| `assert` | `ok` + inline `msg: "All assertions passed"` | `fatal`, playbook stopt | Validatie van gewenste state |
| `fail` | Wordt **overgeslagen** als `when: false` | `fatal`, playbook stopt + custom message | Expliciete failure op condities |

### Belangrijkste lespunten
- `debug` is voor inspectie, niet voor validatie.
- `assert` stopt hard — taken erna worden nooit uitgevoerd.
- `fail` is flexibeler (kan `when:` gebruiken) en geeft een custom message.
- `--check` toont wat er zou gebeuren zonder wijzigingen.
