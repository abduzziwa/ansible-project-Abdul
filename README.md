# README – Vagrant + Ansible Web & Database Servers

## Inhoud

Deze repository bevat een volledige setup voor:

* **Ansible control node**
* **Webserver** (Apache + PHP, met een voorbeeld website)
* **Database server** (MariaDB)

Alle servers worden geprovisioneerd met **Vagrant** en geconfigureerd via **Ansible**.

---

## Vereisten

* [Vagrant](https://www.vagrantup.com/) geïnstalleerd
* [VirtualBox](https://www.virtualbox.org/) geïnstalleerd
* SSH-client (Windows: Git Bash / WSL / PowerShell, Linux/macOS: standaard)
* Minimaal 2 CPU cores en 4GB RAM beschikbaar voor de VM’s

---

## Setup stappen

### 1️⃣ Vagrant VM’s opstarten

Navigeer naar de root van de repository (waar `Vagrantfile` staat) en voer uit:

```bash
vagrant up
```

* Dit zal drie VM’s opzetten:

  * **ansible-Abdul** (control node)
  * **web-Abdul** (webserver)
  * **db-Abdul** (database server)
* Vagrant installeert basis OS en netwerkconfiguratie automatisch.

> Tip: Je kunt de status van de VM’s controleren met `vagrant status`.

---

### 2️⃣ SSH toegang tot Ansible control node

```bash
vagrant ssh ansible-Abdul
```

Je zit nu in de Ansible VM. Hier zal je de playbooks uitvoeren.

---

### 3️⃣ Ansible configuratie controleren

* Zorg dat de bestanden aanwezig zijn:

```bash
ls -l ~/ansible-project
```

* Controleer dat het inventory bestand juist is:

```bash
cat inventory.ini
```

* Test verbinding met de servers:

```bash
ansible all -m ping
```

Je zou een output moeten zien met `SUCCESS => {"ping": "pong"}` voor zowel web- als database server.

---

### 4️⃣ Sudo rechten voor Ansible user

Zorg dat de gebruiker `Abdul` passwordless sudo heeft op alle VM’s.
Als dit nog niet gedaan is, voer de volgende taken uit:

```bash
ssh -i ~/.ssh/id_ansible Abdul@192.168.56.11
sudo su -
echo "Abdul ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/abdul
chmod 440 /etc/sudoers.d/abdul

ssh -i ~/.ssh/id_ansible Abdul@192.168.56.12
sudo su -
echo "Abdul ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/abdul
chmod 440 /etc/sudoers.d/abdul
```

---

### 5️⃣ Ansible playbook uitvoeren

Zorg dat je in de root van de Ansible projectmap zit (`~/ansible-project`) en voer uit:

```bash
ansible-playbook site.yml
```

Wat er gebeurt:

1. **Common setup**: installeert basis utilities op alle servers.
2. **Webserver setup**: installeert Apache + PHP + unzip, zorgt dat `/var/www/html` bestaat, start Apache.
3. **Database setup**: installeert MariaDB en start de service.
4. **Website deploy**: uploadt `site.zip` naar de webserver en extraheert de bestanden naar `/var/www/html` (of `/var/www/html/site` als je DocumentRoot hebt aangepast).

> De playbook zorgt ook dat Apache automatisch herstart als bestanden gewijzigd worden.

---

### 6️⃣ Website controleren

Open een browser op je host-machine en ga naar:

```
http://192.168.56.11/
```

Je zou je PHP-site met je naam moeten zien.

* Controleer MariaDB op de database server:

```bash
ssh -i ~/.ssh/id_ansible Abdul@192.168.56.12
systemctl status mariadb
```

---

### 7️⃣ Extra tips

* **Playbook opnieuw uitvoeren**: Als je iets aanpast aan de site of rollen, kan je `ansible-playbook site.yml` opnieuw uitvoeren.
* **Logging**: Gebruik `-vvv` voor debug output:

```bash
ansible-playbook site.yml -vvv
```

* **Vagrant VM’s stoppen**:

```bash
vagrant halt
```

* **Volledig verwijderen**:

```bash
vagrant destroy -f
```

---

### 8️⃣ Bestandsstructuur

```
ansible-project/
├── Vagrantfile
├── ansible.cfg
├── inventory.ini
├── site.yml
├── site.zip
├── files/
│   └── site.zip
├── roles/
│   ├── common/
│   ├── web/
│   └── db/
└── README.md
```

---

### ✅ Samengevat

1. `vagrant up` → VM’s draaien
2. `vagrant ssh ansible-Abdul` → in Ansible VM
3. `ansible all -m ping` → verbinding testen
4. `ansible-playbook site.yml` → playbook uitvoeren
5. Browser → `http://192.168.56.11/` → PHP website live

---

If you want, I can also **add a small diagram or ASCII architecture** showing the Ansible control node, web, and database servers for clarity in the README. This is nice for professional presentation.

Do you want me to add that?
