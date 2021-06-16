# Role snipeit

Role nasadí jednu nebo více webových aplikací Snipe-IT.

Předpoklady:

- Připravený webový prostor
- Připravená MariaDB/MySQL databáze
- `composer` nainstalovaný v `/usr/local/bin`

Všechny tyto požadavky řeší role `webhosting`.
Role neví o případném HA podkladu. Lze nasazovat pouze na jeden uzel.

Pro nasazení nové verze je nutné nastavit proměnnou `update` na true například na příkazové řádce: `-e update=true`.


## Proměnné

Parametry role jsou definovány ve slovníku `snipeit`. Tato proměnná má následující položky:

| Proměnná       | Povinná | Výchozí     | Popis |
| -------------- | ------- | ----------- | ----- |
| repo           | ne      | `https://github.com/snipe/snipe-it` | master git repozitář s Snipe-IT projektem |
| version        | ne      | master      | verze aplikace instalovaná skrz git |
|                |         |             |       |
| instances      | ano     |             | Pole slovníků definující jednotlivé instance aplikace snipe-it |
| .user          | ne      | `www-data`  | Uživatel vlastnící soubory webu |
| .group         | ne      | `www-data`  | Skupina vlastníka souborů webu |
| .webroot       | ano     |             | Cesta k webroot adresáři, kde je web umístěn |
|                |         |             |       |
| .site          | ne      |             | Slovník s konfigurací webových aspektů aplikace |
| ..domain       | ano     |             | Doména webové aplikace (např. snipeit.firma.tld) |
| ..url          | ano     |             | URL webové aplikace (např. https://snipeit.firma.tld) |
| ..key          | ano     |             | Secret pro šifrování citlivých dat |
|                |         |             |       |
| .database      | ano     |             | Slovník pro konfiguraci přístupu do databáze |
| ..host         | ne      | 127.0.0.1   | Adresa nebo doménové jméno databázového serveru |
| ..name         | ano     |             | Jméno databáze |
| ..user         | ano     |             | Databázový uživatel |
| ..password     | ano     |             | Heslo databázového uživatele |
| ..dump_path    | ne      | /var/backups/{{ database.name }}_preupgrade.sql | Cesta k souboru zálohy prováděné před upgrade schéma databáze |
|                |         |             |       |
| .mail          | ne      |             | Slovník pro nastavení integrace s mail serverem |
| ..server       | ne      | localhost   | Adresa mail serveru |
| ..port         | ne      | 25          | SMTP port mail serveru |
| ..tls          | ne      | none        | Zapne TLS Šifrování. Může být nastaveno v režimu 'tls' nebo 'starttls' |
| ..username     | ne      |             | Uživatel pro SMTP autentizaci |
| ..password     | ne      |             | Heslo pro SMTP autentizaci |
| ..from_mail    | ne      |             | E-mailová adresa FROM |
| ..from_name    | ne      |             | Jméno FROM |
| ..replyto_mail | ne      |             | E-mailová adresa REPLYTO |
| ..replyto_name | ne      |             | Jméno REPLYTO |


## Příklad konfigurace

```yaml
snipeit:
  version: 'v4.9.4'
  instances:
    - user: 'w_inventory'
      group: 'w_inventory'
      webroot: '/srv/www/sites/c_lws/w_inventory/www'
      database:
        name: 'w_inventory'
        user: 'w_inventory'
        password: '{{ vault_webdev_inventory_dbpasswd }}'
      mail:
        server: 'mail.logicworks.cz'
        port: 465
        tls: 'tls'
        username: 'inventory@logicworks.cz'
        password: '{{ vault_snipeit_mail_password }}'
        from_mail: 'inventory@logicworks.cz'
        from_name: 'Sensei Inventory'
        replyto_mail: 'sysadmins@logicworks.cz'
        replyto_name: 'Logicworks Sysadmins'
      site:
        key: '{{ vault_snipeit_app_key }}'
        domain: 'inventory.lws.mit.etn'
        url: 'https://inventory.lws.mit.etn'

```
