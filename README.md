# CISCO_helper
## Содержание
1. <a href=#1>Предупреждение</a>
2. <a href=#2>Начальная настройка</a>
3. <a href=#3>Использование SSH</a>
4. <a href=#4>Использование Telnet</a>
5. <a href=#5>Настройка EIGRP</a>
6. <a href=#6>Настройка OSPF</a>
7. <a href=#7>Настройка Агрегирования (PAgP, LAсP, Static)</a>
8. <a href=#8>Настройка DHCP</a>
9. <a href=#9>Настройка SVI</a>
10. <a href=#10>Настройка HSRP</a>
11. <a href=#11>Настройка VPN</a>
12. <a href=#12>Настройка PVST + Rapid-PVST</a>
13. <a href=#13>Настройка Port-Security</a>
14. <a href=#14>Настройка Spanning-tree</a>
15. <a href=#15>Начальная подготовка для использования IPv6 на маршрутизаторе</a>
16. <a href=#16>Как сделать EUI64?</a>
17. <a href=#17>Настройка EIGRP_v6(EIGRP с использованием IPv6)</a>
18. <a href=#18>Настройка OSPF_v6 (OSPF с использованием IPv6)</a>
19. <a href=#19>Настройка DHCP_v6</a>
20. <a href=#20>Настройка NAT-PT</a>


## <p name =1>Предупреждение </p>
${VARIABLE} -> переменная


## <p name =2>Начальная настройка </p>
config:
  1. no ip domain-lookup
  2. hostname ${Device}
  <br> Device -> название устройства
  4. ip domain-name ${domain}
  <br> domain -> название домена
  6. crypto key generate rsa
      <br> 2048 // Для использования SSH - 1.9
  5. line vty 0 4
<br>5.1. transport input {ssh/all/telnet}
<br>5.2. login local
      * ssh - использование ssh
      * all - использование ssh и telnet
      * telnet - использование только telnet
   6. username ${USERNAME} privilege {0-15} secret ${PASSWORD}
   <br> USERNAME -> логин
   <br> PASSWORD -> пароль от SSH
   </br> Подсказка: 
        * privilege level 0 — это команды disable, enable, exit, help и logout, которые работают во всех режимах
        * privilege level 1 — Это команды пользовательского режима, то есть как только вы попадаете на циску и увидите приглашение Router> вы имеете уровень 1.
        * privilege level 15 — Это команды привилегированного режима, вроде, как root в Unix'ах


 ##  <p name =3>Использование SSH</p>
 
 ssh -l ${USERNAME} ${IP_ADDRESS_DEVICES}
 * USERNAME - логин
 * IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение
 
 ##  <p name =4>Использование Telnet</p>
 
 telnet ${IP_ADDRESS_DEVICES}
 * IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение
 
 ##  <p name =5>Настройка EIGRP </p>
  1. router eigrp ${PROCCESS_ID}
  2. no auto-summary
  3. network ${IP_ADDRESS} ${WILD_CARD_MASK}
  4. passive-interface ${INT}


  ##  <p name =6>Настройка OSPF </p>
  1. router ospf ${LOCAL_ID}
  2. network ${IP_ADDRESS} ${WILD_CARD_MASK} area ${NUMBER}
  3. *default-information originate* // Если сеть указывает на маршрут по умолчанию


  ##  <p name =7>Настройка Агрегирования </p>
  <br>config: 
  * ip range ${TYPE_INTERFACES}${BEGIN}-${END}
  * channel-group ${ID_GROUP} mode {active/passive/desirable/auto/on}
    <br>TYPE_INTERFACES -> тип интерфейса которому идет подключение, например Gigabyte, FastEthernet...
    <br>BEGIN -> интерфейс с которого необходимо начать
    <br>END -> интерфейс на котором надо закончить
    <br>Пример команды: ip range f0/1-24

  ### Настройка PAgP (Port Aggregation Protocol)
  1. необходимо зайти на интерфейсы: <br> ip range ${TYPE_INTERFACES}${BEGIN}-${END}
  2. необходимо прописать channel-group: <br> channel-group ${ID_GROUP} mode {desirable/auto}
  
  <br> Если на одной стороне было установлено **auto**, необходимо прописать **desirable**
  <br> Если на одной стороне было установлено **desirable**, можно прописать либо **desirable**, либо **auto**

  ### Настройка LAcP (Link Aggregation control Protocol)
  1. необходимо зайти на интерфейсы: ip range ${TYPE_INTERFACES}${BEGIN}-${END}
  2. необходимо прописать channel-group: channel-group ${ID_GROUP} mode {passive/active}
  
  <br> Если на одной стороне было установлено **passive**, необходимо прописать **active**
  <br> Если на одной стороне было установлено **active**, можно прописать либо **passive**, либо **active**

  ### Настройка Static
  1. необходимо зайти на интерфейсы: ip range ${TYPE_INTERFACES}${BEGIN}-${END}
  2. необходимо прописать channel-group: channel-group ${ID_GROUP} mode on

  **ВАЖНО** *ВСЕ ОСНОВНЫЕ ПАРАМЕТРЫ НА ФИЗИЧЕСКИХ ИНТРЕФЕЙСАХ ДОЛЖНЫ СОВПАДАТЬ(SPEED, DUPLEX)*
  
  ##  <p name =8>Настройка DHCP</p>
  <br>config
  * ip dhcp pool ${NAME_DHCP_POOL}
    <br> dhcp:
   network ${IP_ADDRESS_POOL} ${MASK}
  * default-router ${IP_DEFAULT_GETWAY}
  * dns-server ${IP_DNS_SERVER} // Может быть что-то другое, но смысл остается таким же
    <br>
      - ip dhcp excluded-address ${IP_ADDRESS_POOL}
  
  
  ##  <p name =9>Настройка SVI  </p>
  <br>config:
  * int vlan ${ID_VLAN}
  * ip address ${IP_ADDRESS} ${MASK}
  
  ##  <p name =10>Настройка HSRP</p>
  <br> config:
  *int vlan ...*:
  * standby ${ID_STANDBY_GROUP} ip ${IP_VIRTUAL_DEFAULT_GATEWAY}
  * standby ${ID_STANDBY_GROUP} priority ${NUM}
  * standby ${ID_STANDBY_GROUP} preemt // Если необходимо указать

  ## <p name=11>Настройка VPN</p>
  <br> config:
  * int tunnel ${NUM_INT}
  * ip addr ${IP_ADDR} ${MASK}
  * tunnel mode gre ip
  * tunnel destination ${IP_DESTIONATION}
  * tunnel source ${TYPE_INTERFACES/IP_ADDR}
  
  ## <p name=12>Настройка PVST + Rapid-PVST</p>
  Для того чтобы настраивать немного теории. PVST -> это Per vlan STP, т.е. это говорит о том, что для каждого VLAN строится своя STP ветка. По умолчанию всего один STP для всех VLAN по протколу STP(то есть выбирается самый наименьший мак адресс). А чтобы сделать PVST надо нащ
  
  
