# MacOS Setup

MacOS heeft het voordeel dat het gebaseerd is op Unix/BSD, door gebruik te maken van de ingebouwde terminal (of vervanger zoals iterm2) hebben we niet zoiets als WSL nodig.

## Homebrew

Veel tools die we hierna installeren kunnen via HomeBrew geinstalleerd worden. Daarom moet eerst HomeBrew op het systeem aanwezig zijn.
Installeer het met het volgende commando:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Terminal (optioneel)

Wezterm en iTerm2 zijn terminal clients met wat meer mogelijkheden dan de ingebouwde terminal client.

```zsh
brew install wezterm
```

```zsh
brew install iterm2
```

## GitHub

Voor onze code gaan we GitHub gebruiken. Ga in een browser naar github.com en log in of maak een account aan als je dat nog niet hebt.

## Installatie OVFTool

Op [deze](https://developer.broadcom.com/tools/open-virtualization-format-ovf-tool/latest) pagina is de binary van OVFtool te vinden. Download de juiste versie.
Pak de zip file uit en installeer deze DMG op je systeem. Het kan zijn dat je permissies moet geven om deze op te mogen starten.

Voeg na installatie de volgende regel toe aan .zshrc in je home directory

```zsh
export PATH="$PATH:/Applications/VMware OVF Tool/"
```

## PIP

PIP hebben we nodig om Ansible te installeren.

```zsh
curl -o get-pip.py https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py
```

## Ansible

Wanneer pip succesvol is geinstalleerd kan Ansible worden geinstaleerd.

```zsh
python3 -m pip install --upgrade pip
python3 -m pip install ansible
```

## Terraform

Voor de installatie van Vagrant gebruiken we ook weer brew

```zsh
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

## VScode

Om o.a. de Ansible code te kunnen bewerken is het handig als er een editor op je systeem staat. VSCode is een handige editor.

```zsh
brew install --cask visual-studio-code
```

## NeoVim

Een andere optie is een editor in de terminal zoals NeoVim.

```zsh
brew install neovim
```
