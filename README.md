# CISCO_helper
## Содержание
1. Предупреждение
2. Начальная настройка
3. Использование SSH
4. Использование Telnet
5. Настройка EIGRP
6. Настройка OSPF
7. Настройка Агрегирования 
8. Настройка DHCP
9. Настройка SVI
10. Настройка HSRP
11. Настройка VPN
12. Настройка PVST + Rapid-PVST
13. Настройка Port-Security
14. Настройка Spanning-tree
15. 
## Предупреждение
${VARIABLE} -> переменная


## Начальная настройка:
config:
  1. no ip domain-lookup
  2. hostname ${Device}
  <br> Device -> название устройства
  4. ip domain-name ${domain}
  <br> domain -> название домена
  6. crypto key generate rsa
      2048 // Для использования SSH - 1.9
  5. line vty 0 4
<br>5.1. transport input {ssh/all/telnet}
<br>5.2. login local
      * ssh - использование ssh
      * all - использование ssh и telnet
      * telnet - использование только telnet
   6. username ${USERNAME} privilege {0-15} secret ${PASSWORD}
   </br> Подсказка: 
        * privilege level 0 — это команды disable, enable, exit, help и logout, которые работают во всех режимах
        * privilege level 1 — Это команды пользовательского режима, то есть как только вы попадаете на циску и увидите приглашение Router> вы имеете уровень 1.
        * privilege level 15 — Это команды привилегированного режима, вроде, как root в Unix'ах


 ## Использование SSH
 
 
 ## Использование Telnet
 
 
 ## Настройка EIGRP
  1. router eigrp ${PROCCESS_ID}
  2. no auto-summary
  3. network ${IP_ADDRESS} ${WILD_CARD_MASK}
  4. passive-interface ${INT}
  ## Настройка OSPF
  1. router ospf ${LOCAL_ID}
  2. network ${IP_ADDRESS} ${WILD_CARD_MASK} area ${NUMBER}
  *3. default-information originate*
  ## Настройка Агрегирования 
  
  ## Настройка DHCP
  
  ## Настройка SVI
  
  ## Настройка HSRP
  
  
