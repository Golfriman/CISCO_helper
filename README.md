# CISCO_helper
## Содержание
0. <a href=#0>КРИК ДУШИ</a>
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
21. <a href=#21>Редистрибьюция маршрутов</a>
22. <a href=#22>SPLIT-HORIZON</a>
 
## <p name=0>КРИК ДУШИ</p>
Я делал это грубо говоря для себя. Все ошибки или недочеты, которые могут тут возникнуть никак не повлияют на меня, но на вас могут. Поэтому **верить** каждому пункту я **не советую**. ***Все сделано только из энтузиаста***.
И да, мне понравились переменные в стиле CMAKE, не вините меня. Если что-то не нравится, можете сделать fork от этого репозитория и развивать так, как вам хочется. Я просто хотел сделать для себя сборник типовых задач, с их решением.

## <p name =1>Предупреждение </p>
* ${...} -> переменная
* {.../...} -> выбор обязательного варианта
* {...-...} -> выбор обязательного варианта из диапозона
* config -> настройка конфигурационного файла
* config-if -> настройка интерфейса
## <p name =2>Начальная настройка </p>
config:
1. no ip domain-lookup
2. hostname ${Device}
  * Device -> название устройства
4. ip domain-name ${domain}
  * domain -> название домена
6. crypto key generate rsa
  * 2048 // Для использования SSH - 1.9
5. line vty 0 4
  <br>5.1. transport input {ssh/all/telnet}
  <br>5.2. login local
    * ssh - использование ssh
    * all - использование ssh и telnet
    * telnet - использование только telnet
6. username ${USERNAME} privilege {0-15} secret ${PASSWORD}
  <ul> 
  <li>USERNAME -> логин
  <li>PASSWORD -> пароль от SSH
   <ul> <li>Подсказка: 
           <ul>
         <li>privilege level 0 — это команды disable, enable, exit, help и logout, которые работают во всех режимах
         <li>privilege level 1 — Это команды пользовательского режима, то есть как только вы попадаете на циску и увидите приглашение Router> вы имеете уровень 1.
         <li>privilege level 15 — Это команды привилегированного режима, вроде, как root в Unix'ах
        </ul>
          </ul>
   </ul>

<h2 name =3>Использование SSH</h2>
ssh -l ${USERNAME} ${IP_ADDRESS_DEVICES}<br>
  <ul>
  <li>USERNAME - логин</li>
  <li>IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение</li>
  </ul>
 
## <h2 name =4>Использование Telnet</h2>
 
 telnet ${IP_ADDRESS_DEVICES}
 * IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение
 
<h2 name =5>Настройка EIGRP </h2>
  <ol>
  <li> router eigrp ${PROCESS_ID}</li>
  <li> no auto-summary </li>
  <li> network ${IP_ADDRESS} ${WILD_CARD_MASK} </li>
  <li>passive-interface ${INT}</li>
  </ol>
  <ul><li> <b><i>ВАЖНО</i></b> необходимо чтобы PROCESS_ID совпадал между другими устройствами, которые участвуют в протоколе адаптивной маршрутизации </li></ul>
  <ul>
  <li>PROCESS_ID      -> уникальный индетификатор процесса, который должен запускается единожды на устройстве
  <li>IP_ADDRESS      -> адрес сети
  <li>WILD_CARD_MASK  -> обратная маска
  <li>INT             -> название интерфейса
  </ul>
<h2 name =6>Настройка OSPF </h2>
  <ol>
  <li>router ospf ${LOCAL_ID}</li>
  <li>network ${IP_ADDRESS} ${WILD_CARD_MASK} area ${NUMBER}</li>
  <li><b>default-information originate</b> // Если сеть указывает на маршрут по умолчанию</li>
  </ol>
  <ul>
  <li>LOCAL_ID       -> уникальный id, но имеет локальную значимость, т.е. на других устройствах можно записывать другой id
  <li>IP_ADDRESS     -> адрес сети
  <li>WILD_CARD_MASK -> обратная маска
  <li>NUMBER         -> номер зоны, имеет важное значение в протоколе OSPF, изначально протокол OSPF работает внутри зоны 0
  </ul>


 <h2 name =7>Настройка Агрегирования </h2>
  <br>config: 
  <ul> 
  <li>int range ${TYPE_INTERFACES}{BEGIN-END}</li>
  <li>channel-group ${ID_GROUP} mode {active/passive/desirable/auto/on}</li>
      <ul>
         <li> TYPE_INTERFACES -> тип интерфейса которому идет подключение, например Gigabyte, FastEthernet...</li>
         <li> BEGIN           -> интерфейс с которого необходимо начать </li>
         <li> END             -> интерфейс на котором надо закончить </li>
         <li> ID_GROUP        -> id группы, которая агрегирует порты. Также должно совпадать с противоположной стороной???
      </ul>
  </ul>
    <br>Пример команды: int range f0/1-24

  ### Настройка PAgP (Port Aggregation Protocol)
  <ol>
  <li> необходимо зайти на интерфейсы: <br> int range ${TYPE_INTERFACES}${BEGIN}-${END}</li>
  <li>необходимо прописать channel-group: <br> channel-group ${ID_GROUP} mode {desirable/auto}</li>
  </ol>
  <br> Если на одной стороне было установлено <b>auto</b>, необходимо прописать <b>desirable</b>
  <br> Если на одной стороне было установлено <b>desirable</b>, можно прописать либо <b>desirable</b>, либо <b>auto</b>

  ### Настройка LAcP (Link Aggregation control Protocol)
  <ol>
  <li>необходимо зайти на интерфейсы: int range ${TYPE_INTERFACES}${BEGIN}-${END}</li>
  <li>необходимо прописать channel-group: channel-group ${ID_GROUP} mode {passive/active}</li>
  </ol>
  <br> Если на одной стороне было установлено <b>passive</b>, необходимо прописать <b>active</b>
  <br> Если на одной стороне было установлено <b>active</b>, можно прописать либо <b>passive</b>, либо <b>active</b>

  ### Настройка Static
  <ol>
  <li>необходимо зайти на интерфейсы: int range ${TYPE_INTERFACES}${BEGIN}-${END}</li>
  <li>необходимо прописать channel-group: channel-group ${ID_GROUP} mode on</li>
  </ol>
  **ВАЖНО** *ВСЕ ОСНОВНЫЕ ПАРАМЕТРЫ НА ФИЗИЧЕСКИХ ИНТРЕФЕЙСАХ ДОЛЖНЫ СОВПАДАТЬ(SPEED, DUPLEX)*
  
  <h2 name =8>Настройка DHCP</h2>
  <br>config
  <ul> 
  <li>ip dhcp excluded-address ${IP_ADDRESS_STATIC}
  <li>ip dhcp pool ${NAME_DHCP_POOL}
  <br>dhcp:
      <ul>
      <li>network ${IP_ADDRESS_POOL} ${MASK}
      <li>default-router ${IP_DEFAULT_GATEWAY}
      <li>dns-server ${IP_DNS_SERVER} // Может быть что-то другое, но смысл остается таким же
        <ul>
          <li>Объяснения:
           <ul>
           <li>NAME_DHCP_POOL    -> название POOL адресов, по которому потом будет присвоение
           <li>IP_ADDRESS_POOL   ->  пространство сети, которое будет раздавать
           <li>MASK              -> обычная маска, которая делает что обычно
           <li>IP_DEFAULT_GATEWAY-> IP шлюза по умолчанию
           <li>IP_DNS_SERVER     -> IP DNS сервера
           <li>IP_ADDRESS_STATIC -> IP, который не должен раздавать DNS-server
            </ul>
        </ul>
    </ul>
    </ul>
  
  
<h2 name =9>Настройка SVI  </h2>
  <br>config:
  <ul>
  <li>int vlan ${ID_VLAN}
  <li>ip address ${IP_ADDRESS} ${MASK}
  </ul>
  
<h2 name =10>Настройка HSRP</h2>
  <br> config:
  *int vlan ...*:
  <ul>
  <li>standby ${ID_STANDBY_GROUP} ip ${IP_VIRTUAL_DEFAULT_GATEWAY}
  <li>standby ${ID_STANDBY_GROUP} priority ${NUM}
  <li>standby ${ID_STANDBY_GROUP} preemt // Если необходимо указать
  </ul>

<h2 name=11>Настройка VPN</h2>
  <br> config:
  <ul>
  <li> int tunnel ${NUM_INT}
  <li> ip addr ${IP_ADDR} ${MASK}
  <li> tunnel mode gre ip
  <li> tunnel destination ${IP_DESTIONATION}
  <li> tunnel source {${TYPE_INTERFACES}/${IP_ADDR}}
  </ul>
  
<h2 name=12>Настройка PVST + Rapid-PVST</h2>
  Для того чтобы настраивать немного теории.
  <ul>
  <li>PVST -> это Per vlan STP, т.е. это говорит о том, что для каждого VLAN строится своя STP ветка.
  <li> По умолчанию всего один STP для всех VLAN по протколу STP(то есть выбирается самый наименьший мак адресс). 
  </ul>
  Чтобы управлять STP веткой, необходимо задавать приоритет для определенного коммутатора (SWITCH).
  <br><code cisco>config: spanning-tree vlan ${ID_VLAN} root primary</code>
  <ul><li> Чтобы поменять PVST на Rapid-PVST необходимо задать следующую команду:</ul>
  <br><code cisco>config: spanning-tree mode rapid-pvst</code>
  
<h2 name=13>Настройка Port-Security</h2>
  <br>config:
  <ul>
  <li> int  ${TYPE_INTERFACES}${BEGIN}
  <li> switchport port-security
  <li> switchport port-security mac-address {sticky / ${MAC_ADDRESS}}
  <ul>
  <ol type="1">
  <li> sticky -> принимает автоматически значение MAC_ADDRESS устройства, который подклчен по данному порту
  <li> ${MAC_ADDRESS} -> статический MAC ADDRESS, который необходимо самому ввести
  </ol>
  <ul>
  <li> switchport port-security maximum ${NUM}
  <li> switchport port-security violation ${TYPE}
  </ul>
  <br> ${TYPE}:
  <ol type="1">
   <li> protect - ...
   <li> restrict - ...
   <li> shutdown - ...
 </ol>
 </ul>

<h2 name=14>Настройка Spanning-tree</h2>
  По факту тут все команды относящиеся к spanning-tree. К примеру это рассматривалось при настройке PVST.
  <br> Но также необходимо рассматривать следующие команды:
  <ol>
  <li>Настройка типов соединения между комутаторами. 
  <ul> config-if: spanning-tree link-type ${TYPE}
    <li>${TYPE}: 
    <ul>
    <li> point-to-point
     <li> shared
      </ul>
    </ul>
  <li> Настройка bpduguard.
  <ul>
    <li> config-if: spanning-tree bpduguard ${STATUS}
    <ul>
      <li> ${STATUS} -> disable/enable
      </ul>
    </ul>
  <li> Настройка portfast
  <ul> 
    <li> config-if: spanning-tree portfast ${STATUS}
    <ul>
      <li> ${STATUS} -> disable/trunk
      </ul>
    </ul>
    </ol>
  
<h2 name=15>Начальная подготовка для использования IPv6 на маршрутизаторе</h2>
  config:
  <ul> 
    <li> ipv6 unicast-routing
  </ul>
<br> Без этой команды не будут работать большиинство команд IPv6
<h2 name=16>Как сделать EUI64?</h2>
  <ol>
    <li> Берем MAC-адрес конечного узла (00E0.B072.39B4) 
  <li> Разрезаем мак адрес пополам: [00E0.B0] [72.39B4]
  <li> Вставляем FF:FE между двумя частями получаем 00E0:B0FF:FE72:39B4
  <li> 7 бит начиная слева необходимо инвертировать то есть получаем: 02E0:B0FF:FE72:39B4
  <li> Остальные 64 бита IPv6 адреса необходимо брать из варианта
  </ol>
  <br>
  Example<br>
  MAC:ㅤAAAA.BBBB.CCCC<br>
  AAAA:BBFF:FEBB:CCCC<br>
   ║<br>
   Vㅤ 5 6 7 8<br>
   A = 1 0 1 0 => 1000 = 8<br>
   ㅤㅤㅤㅤㅤㅤㅤ         ║<br>
   ㅤㅤㅤㅤㅤㅤㅤㅤㅤㅤㅤㅤV<br>
   ㅤㅤㅤㅤㅤㅤㅤㅤㅤㅤㅤ A8AA:BBFF:FEBB:CCCC<br>
  
  <ul> <li>В итоге получается ХХХХ:XXXX:XXXX:XXXX:02E0:B0FF:FE72:39B4, где ХХХХ:XXXX:XXXX:XXXX часть задается по условиям задания. </ul>

   <h2 name=17>Настройка EIGRP_v6(EIGRP с использованием IPv6)</h2>
   config: ipv6 router eigrp ${ID_PROCESS}
   <ul>
   <li>no shut
   <li>eigrp router-id A.B.C.D //Если нет IPv4 на интерфейсах нет ни одного заданного IPv4 адреса
   </ul>
   <br>config:
   <ul>
   <ul><li><i>Необходимо приделать ко всем интерфейсам, которые участвуют в протоколе EIGRP</i></li></ul>
   <li>int ${TYPE_INTERFACES}${BEGIN}
   <li>ipv6 eigrp ${ID_PROCESS}
   </ul>
   <h2 name=18>Настройка OSPF_v6 (OSPF с использованием IPv6)</h2>
   config: ipv6 router ospf ${ID_PROCESS}
   <ul>
   <li>router-id A.B.C.D //Если нет IPv4 на интерфейсах нет ни одного заданного IPv4 адреса
