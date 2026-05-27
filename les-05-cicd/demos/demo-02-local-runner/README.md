# Demo 2: Local Runner in Actie

**Doel:** Studenten laten zien hoe een self-hosted GitHub Actions runner op hun eigen laptop een Ansible-playbook uitvoert dat nginx installeert.

## Concept
- Je installeert een GitHub Actions **self-hosted runner** op je laptop.
- Bij elke push naar `main` draait de runner:
  1. `yamllint` — controleert YAML-syntax
  2. `ansible-playbook --syntax-check` — controleert Ansible-syntax
  3. `ansible-playbook --check --diff` — dry-run (laat zien wat er zou veranderen)
  4. `ansible-playbook` — voert het playbook echt uit (installeert nginx!)
- Het playbook installeert nginx, start de service, plaatst een aangepaste `index.html`, en test of de pagina bereikbaar is.

## Stappenplan demo

### 1. Repository aanmaken
```bash
git clone git@github.com:<jouw-username>/demo-02-runner.git
cd demo-02-runner
cp -r /pad/naar/demo-02-local-runner/* .
git add . && git commit -m "Start: nginx playbook + self-hosted runner workflow"
git push
```

### 2. Self-hosted runner installeren
```bash
# Ga op GitHub naar: Settings → Actions → Runners → New self-hosted runner
# Kies Linux en je architectuur (meestal x64)
# Volg de instructies op het scherm. Samengevat:

mkdir actions-runner && cd actions-runner

# Download het package (URL uit GitHub interface)
curl -o actions-runner-linux-x64-2.322.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.322.0/actions-runner-linux-x64-2.322.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.322.0.tar.gz

# Configureer de runner
./config.sh --url https://github.com/<jouw-username>/<repo> --token <TOKEN>

# Start de runner
./run.sh
# ⚠️ Laat deze terminal open staan tijdens de demo!
```

### 3. Pipeline triggeren
```bash
# Doe een wijziging (pas de welkom_tekst aan in install-nginx.yml)
git add ansible/install-nginx.yml
git commit -m "Update: welkomsttekst aangepast"
git push
```

### 4. Pipeline volgen
- Ga naar de **Actions**-tab op GitHub.
- Klik op de lopende workflow en laat studenten elke stap zien.

### 5. Opruimen
```bash
sudo systemctl stop nginx
# Druk Ctrl+C in de runner-terminal
```

## Wat leg je uit
| Stap | Wat gebeurt er? | Waarom belangrijk? |
|------|----------------|-------------------|
| `runs-on: self-hosted` | De job draait op jouw laptop, niet op GitHub's servers | Laat zien dat CI/CD overal kan draaien |
| `--check --diff` | Dry-run: toont wijzigingen zonder iets te doen | Veiligheid voor productie |
| `gather_facts: yes` | Verzamelt systeeminformatie (OS, distro) | Maakt playbook OS-onafhankelijk |
| `assert` aan het eind | Test of nginx echt de juiste pagina serveert | Infrastructure testing in actie |
