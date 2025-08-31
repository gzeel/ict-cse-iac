# Linux

Deze handleiding gaat uit van Ubuntu 24.04. Wil je iets anders? Dan zul je sommige commando's anders moeten gebruiken.

## Pre-req

Op dit moment moet je VM's die in je skylab aanmaakt handmatig voorzien van de juiste netwerkinstellingen. Als je DHCP actief hebt op je pfsense kun je dit vrij eenvoudig doen.
Log via de Skylab Remote Console in op het systeem met de student gebruiker. Voer dan het volgende commando uit:

```bash
sudo vi /etc/netplan/99-netcfg-vmware.yaml
```

Als sudo wachtwoord gebruik je ook het wachtwoord van student.

Haal dan de onderste 2 regels weg, dus het IP adres en de regel met `addresses`. Verander `dhcp4: no` in `dhcp4: yes`.
Pas hierna de configuratie toe:

```bash
sudo netplan apply
```

en reboot de vm.

Log nu via ssh in op het systeem, gebruik bv Windows Terminal of Putty hiervoor.
Installeer de pakketten git en unzip op het systeem:

```bash
sudo apt install git unzip curl
```

## SSH

We gaan passwordless SSH logins gebruiken vanaf je laptop en ontwikkelmachine richting de VM's die je aan gaat maken. Hiervoor moet je 3 ssh-keypairs aanmaken. 1 Om vanaf je laptop in te loggen naar je ontwikkelmachine, en 1 om vanaf je ontwikkelmachine in te loggen op de VM's die we met Terraform gaan maken. Het laatste keypair is voor Azure.
Maak daarom een ssh keypair aan voor de student user (of voor een andere user die gaat gebruiken) op je Linux ontwikkel VM. We gebruiken een ED25519 key omdat deze kort is.

Herhaal dit commando dus 3x en gebruik verschillende namen. Bijvoorbeeld devhost, skylab en azure

```bash
ssh-keygen -t ed25519
```

In de directory /home/student/.ssh staan nu 6 bestanden. 3 private en 3 public keys.
De 'devhost' private key kopieer je naar je eigen laptop. Dan kun je passwordless inloggen via een terminal of VSCode. Zet daarom deze private key file in "C:\Users\je gebruikersnaam\.ssh\" Dit kun je bijvoorbeeld doen door een nieuwe file in die directory te maken met de inhoud van de file /home/student/.ssh/devhost

Zet vervolgens de inhoud van /home/student/.ssh/devhost.pub in /home/student/.ssh/authorized_keys bijvoorbeeld dus zo:

```bash
student@ubuntu:~/.ssh$ cat authorized_keys
ssh-ed25519 AAAAHierstaateensupergeheimesshed25519key/blabla student@lokale-laptop
```

Let op: het bestand /home/student/.ssh/authorized_keys moet alleen write rechten voor de owner hebben (tip: chmod 400 authorized_keys)

Als laatste upload je de azure publieke key naar Azure. Zie hiervoor https://learn.microsoft.com/en-us/azure/virtual-machines/ssh-keys-portal en dan 'Upload a ssh key'.

## GitHub

Voor onze code gaan we GitHub gebruiken. Ga in een browser naar github.com en log in of maak een account aan.

## Installatie OVFTool

Download OVFTool via de volgende link: <https://github.com/rgl/ovftool-binaries/raw/main/archive/VMware-ovftool-4.6.3-24031167-lin.x86_64.zip> .
Voer de volgende commando's uit vanuit de directory waar je de zip hebt gedownload:

```bash
unzip VMware-ovftool-<versie>-lin.x86_64.zip
```

In de files directory doe je het volgende :

```bash
sudo mv ovftool vmware-ovftool
```

```bash
sudo mv vmware-ovftool /usr/bin/
```

```bash
sudo chmod +x /usr/bin/vmware-ovftool/ovftool.bin
```

```bash
sudo chmod +x /usr/bin/vmware-ovftool/ovftool
```

Voer nu het volgende commando uit, als je een andere gebruiker dan de default user 'student' gebruikt moet je dit aanpassen.

```bash
sed -i '$ a\PATH=$PATH:/home/student/.local/bin:/usr/bin/vmware-ovftool' ~/.bashrc
```

## PIP

We installeren Ansible via PIP. Daardoor is het noodzakelijk dat PIP op het systeem staat.

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common python3-pip pipx
```

Voeg het juiste path toe aan je profiel

```bash
pipx ensurepath
```

## Terraform

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

```bash
sudo apt-get update && sudo apt-get install terraform
```

## Ansible

Wanneer pip succesvol is geinstalleerd kan Ansible worden geinstaleerd.

```bash
pipx install --include-deps ansible
```

```bash
pipx install ansible-lint
```

Log als laatste actie uit en weer opnieuw in.
