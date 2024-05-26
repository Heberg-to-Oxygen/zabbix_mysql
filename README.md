# Zabbix MySQL

- Author : DJERBI Florian
- Object : Monitoring Mysql on Zabbix 
- Creation Date : 05/23/2024
- Modification Date : 05/26/2024


## Besoins
Informations sur le volume des DB, le nombre de connexions utilisateurs et le nombre de requêtes en cours depuis X temps.

**Attention !!** Le template peut rajouter très facilement 100 à 500 Items et +1000 Triggers sur la VM, suivant le nombre d'utilisateur et de DB que vous avez.


## Informations
### Template
- 2 Items:
    - Nombre de requête en cours depuis X secondes (*défaut:3H*)
    - Nombre de requête en cours depuis X secondes (*défaut:6H*)
- 2 Triggers:
    - Requête en cours depuis plus X heures (*défaut:3H*)
    - Requête en cours depuis plus X heures (*défaut:6H*)
- 2 Screens:
    - Page dédiée aux graphiques de toutes les connexions des utilisateurs (*lié avec discovery*)
    - Page dédiée aux graphiques de tous les volume DB (*lié avec discovery*)
- 2 Discovery:
    - Database size: nom de toutes les databases (*check toutes les 1h*)
        - 1 Item: volume de la DB (*check toutes les 5m*)
        - 1 Graphique: suivi du volume (*gestion des unitées "Ko, Mo, Go"*)
        - 2 Triggers: 
            - si volume DB > X Go (*défaut:3*)
            - si volume DB > X Go (*défaut:5*)
    - Database user: nom de tous les utilisateurs (*check toutes les 1h*)
        - 1 Item: nombre connexion sur DB (*check toutes les 5m*)
        - 1 Graphique: suivi du nombre de connexion
        - 2 Triggers:
            - maximum connexion > X (*défaut:15*)
            - maximum connexion > X (*défaut:30*)

### Mise en place
Ajouter le [Template](./zbx_export_templates.json) sur l'intaerface Zabbix dans 'Configuration => Templates => Import' puis le lié sur les Hosts.

Un fichier de configuration pour les Items Key dois être préalablement mit sur le serveur : */etc/zabbix/zabbix_agent2.d/plugins.d/zabbix_mysql/[userparameter_mysql.conf](./userparameter_mysql.conf)*

**Il est grandemment préférable de faire un utilisateur spécial en read-only !** (*défaut:zabbix*)

**Attention !!**
Il faut configurer les macros pour Mysql.
{$MYSQL.PASSWORD}, {$MYSQL.USER}, {$MYSQL.DSN}, {$MYSQL.PORT}

### Utilisateur Database
**Il est grandemment préférable de faire un utilisateur spécial en read-only !** (*défaut:zabbix_mysql*)
``` bash
CREATE USER 'zabbix_mysql'@'%' IDENTIFIED BY 'i1wIp%2lKXk3RwPi';
GRANT SHOW DATABASES ON *.* TO 'zabbix_mysql'@'%';
GRANT SHOW VIEW ON *.* TO 'zabbix_mysql'@'%';
GRANT SELECT ON mysql.user TO 'zabbix_mysql'@'%';
FLUSH PRIVILEGES;
```
