#### LAB

Deze twee opdrachten maak je waar mogelijk in tweetallen. 1 persoon maakt de VM's op ESXi en de andere persoon maakt ze in Azure. Werk samen aan de code.

> Opdracht 1 :

A. (ESXi) Maak een terraform manifest voor 1 Ubuntu VM met 1 vcpu en 1024MB geheugen. Je zult zien dat de VM start en dat via de remote console van ESXi een prompt kunt zien. Maar je kunt er nog niks mee.. Daarom mag je de VM verwijderen. De code bewaar je wel.

B. (Azure) Maak een terraform manifest voor 1 Ubuntu VM, gebaseerd op de Standard_B2ats_v2 sizing.

> Opdracht 2 :

A. (ESXi) Maak een terraform deployment file waarin je in totaal 3 Ubuntu VM's deployed met 1 vcpu en 2048MB geheugen, met de volgende kenmerken:

> - Je hebt het Ubuntu 24.04 cloudimage gebruikt
> - Er is een resource voor 2 Ubuntu VM's die de naam webserver hebben
> - Er is een resource voor 1 Ubuntu VM die de naam databaseserver heeft.
> - Via cloudinit maak je op de 3 vm's een gebruiker aan met sudo rechten zonder dat er alsnog een wachtwoord wordt gevraagd.
>   (De volgende criteria zoek je zelf op in de voorbeelden van de provider, zie voor de link hieronder)
> - Via cloudinit zet je je public ssh-key (let op gebruik je ED25519 key) op de 3 vm's gezet
> - Via cloudinit installeer je de packages wget en ntpdate.
> - Het ip adres van elke machine komt in een bestand op je beheer systeem.
> - Maak in je Terraform manifest gebruik van variabelen waar dit kan. Zet deze variabelen in een apart bestand.
>
> Gebruik hiervoor de voorbeelden in de IAC Git repository en de voorbeelden in de [Git Repository van de ESXI-terraform provider](https://github.com/josenk/terraform-provider-esxi/tree/master/examples)

B. (Azure) Maak 1 terraform manifest waarin je 2 Ubuntu 24.04 VM in Azure deployed met de volgende kenmerken:

> - De VM is van het type "Standard_B2ats_v2"
> - Je eerder geuploade SSH key wordt gebruikt.
> - Je VM heeft een publiek IP adres
> - Je maakt de user 'iac' aan.
> - Via CloudInit wordt er een bestand 'hello.txt' in /home/iac geplaatst met de inhoud 'Hello World'
> - Het ip adres van elke machine komt in een bestand op je beheer systeem.
> - Het moet dus 1 manifest zijn waarin 2 dezelfde VM's aangemaakt worden.
> - Maak in je Terraform manifest gebruik van variabelen waar dit kan. Zet deze variabelen in een apart bestand.

Gebruik de volgende [Git repository](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples/virtual-machines/linux) met voorbeelden van Hashicorp voor Azure.

Maak een korte video waarin je laat zien dat je vanuit de VM op ESXi van opdracht 2A een SSH sessie kunt starten naar de Azure VM (opdracht 2B)
