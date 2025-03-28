#### LAB

> Opdracht :
> Maak een complete deployment waarin je een Azure VM en ESXi VM combineert en je een hybrid cloud situatie maakt. Gebruik de stof van de afgelopen lessen. De deployment is compleet geautomatiseert, inclusief het aanmaken van VM's en andere resources in Azure. Je maakt op beide omgeving een gebruiker 'testuser' aan, via Ansible of via Terraform. De testuser kan inloggen van de ESXI VM naar de Azure VM, het plaatsen van de benodigde SSH keys is geautomatiseerd. Op beide systemen draait een "Hello World" Docker container en je gebruikt een zelfgemaakte ansible-galaxy role om docker te installeren. De omgeving kan automatisch via CI/CD uitgerold worden (dit hoeft alleen voor ESXi, want je hebt niet genoeg rechten hiervoor op Azure)
