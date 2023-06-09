# CISCO_helper
## Содержание
0. <a href=#0>КРИК ДУШИ</a>
1. <a href=#1>Расшифровка</a>
2. <a href=#2>Начальная настройка</a>
3. <a href=#3>Использование SSH</a>
4. <a href=#4>Использование Telnet</a>
5. <a href=#5>Настройка EIGRP</a>
6. <a href=#6>Настройка OSPF</a>
7. <a href=#7>Настройка Агрегирования (PAgP, LAсP, Static)</a>
8. <a href=#8>Настройка DHCP</a>
9. <a href=#8.1>Маршрутизация на L3 Switch(3650, 3560) </a>
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
23. <a href=#23>Процесс загрузки маршрутизатора</a>
24. <a href=#24>BGP</a>
25. <a href=#25>Доп. инфо</a>
 
## <p name=0>КРИК ДУШИ</p>
Я делал это грубо говоря для себя. Все ошибки или недочеты, которые могут тут возникнуть никак не повлияют на меня, но на вас могут. Поэтому **верить** каждому пункту я **не советую**. ***Все сделано только из энтузиаста***.
И да, мне понравились переменные в стиле CMAKE, не вините меня. Если что-то не нравится, можете сделать fork от этого репозитория и развивать так, как вам хочется. Я просто хотел сделать для себя сборник типовых задач, с их решением.

## <p name =1>Расшифровка </p>
* ${...} -> переменная
* {.../...} -> выбор обязательного варианта
* {...-...} -> выбор обязательного варианта из диапозона
* config -> настройка конфигурационного файла
* config-if -> настройка интерфейса
* config-router-> настройка протокола маршрутизации
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
<h3>Теория</h3>
Secure Shell - протокол для защиты передаваемых данных между клиентом и сервером.
<h3>Практика</h3>
ssh -l ${USERNAME} ${IP_ADDRESS_DEVICES}<br>
  <ul>
  <li>USERNAME - логин</li>
  <li>IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение</li>
  </ul>
 
## <h2 name =4>Использование Telnet</h2>
<h3>Теория</h3>
Telnet - протокол текстовой связи через TCP/IP без защиты.
<h3>Практика</h3>
 telnet ${IP_ADDRESS_DEVICES}
 * IP_ADDRESS_DEVICES - ip интрефейса к которому идет подключение
 
<h2 name =5>Настройка EIGRP </h2>
<h3>Теория</h3>
Enchanced Interior Gateway Routing Protocol - улучшенная версия IGRP. Основан на методе расстояния вектора (distance-vector) и использует алгоритм Дейкстры для поиска наилучших маршрутов. Использует алгоритм DUAL (Diffusing Update Algoritm), котороый кроме расстояния учитывает пропускную способность и задержку. 
<h3>Практика</h3>
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
<h3>Теория</h3>
Open Shortest Path First - протокол маршрутизации для нахождения оптимальных маршрутов в сети. В отличии от EIGRP основан на методе состояния связей (link-state).
<h3>Практика</h3>
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
  <h3>Теория</h3>
  Объединяет несколько физических портов в одну логическую группу. Повышает пропускную способность и обеспечивает избыточность. Также повышает надежность связи. Работает только на устройствах Cisco. 
  PAgP имеет два режима работы: "auto" (автоматический) и "desirable" (желательный). В режиме "auto" порт ожидает, что соседний порт активирует агрегацию, а в режиме "desirable" порт активно инициирует процесс агрегации соседнему порту.
  <h3>Практика</h3>
  <ol>
  <li> необходимо зайти на интерфейсы: <br> int range ${TYPE_INTERFACES}${BEGIN}-${END}</li>
  <li>необходимо прописать channel-group: <br> channel-group ${ID_GROUP} mode {desirable/auto}</li>
  </ol>
  <br> Если на одной стороне было установлено <b>auto</b>, необходимо прописать <b>desirable</b>
  <br> Если на одной стороне было установлено <b>desirable</b>, можно прописать либо <b>desirable</b>, либо <b>auto</b>

  ### Настройка LAcP (Link Aggregation control Protocol)
  <h3>Теория</h3>
  То же, что и PAgP, только на всех устройствах помимо Cisco.
  <h3>Практика</h3>
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
  <h3>Теория</h3>
  Dynamic Host Configuration Protocol - это протокол, используемый в компьютерных сетях для автоматической конфигурации сетевых параметров устройств, таких как IP-адрес, подсеть, шлюз по умолчанию, DNS-серверы и другие параметры сети. DHCP облегчает процесс подключения устройств к сети и автоматической настройки сетевых параметров без необходимости вручную вводить каждый параметр.
  SLAAC (Stateless Address Autoconfiguration) - это метод автоматической настройки IPv6-адресов на устройствах в сети без использования DHCP (Dynamic Host Configuration Protocol). В SLAAC процесс настройки адреса основывается на информации, полученной от сетевого маршрутизатора.
  <h3>Практика</h3>
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
  
<h2 name=8.1>Маршрутизация на L3 Switch </h2>
config: ip routing

<h2 name =9>Настройка SVI  </h2>
<h3>Теория</h3>
Switched Virtual Interface - это виртуальный интерфейс, который создается на коммутаторе (свитче) для предоставления возможности маршрутизации и коммуникации с другими сетевыми устройствами на уровне Layer 3 (сетевого уровня) внутри VLAN (Virtual LAN).
<h3>Практика</h3>
  <br>config:
  <ul>
  <li>vlan ${ID}
  <li>int vlan ${ID_VLAN}
  <li>ip address ${IP_ADDRESS} ${MASK}
  </ul>
  
<h2 name =10>Настройка HSRP</h2>
<h3>Теория</h3>
Hot Standby Router Protocol - это протокол горячей резервирования маршрутизаторов, который используется для создания виртуального шлюза по умолчанию (default gateway) в сетях с множеством маршрутизаторов. HSRP обеспечивает высокую доступность и отказоустойчивость шлюза по умолчанию, позволяя настроить несколько маршрутизаторов в группу, из которых один является активным, а остальные - резервными. HSRP использует виртуальный IP-адрес и виртуальный MAC-адрес, которые ассоциируются с группой маршрутизаторов. Когда клиент отправляет пакет на виртуальный IP-адрес, активный маршрутизатор HSRP получает пакет, обрабатывает его и отправляет ответные пакеты обратно клиенту. Если активный маршрутизатор становится недоступным, резервный маршрутизатор автоматически принимает виртуальный IP-адрес и продолжает обрабатывать трафик, обеспечивая непрерывность работы сети.
<h3>Практика</h3>
  <br> config:
  *int vlan ...*:
  <ul>
  <li>standby ${ID_STANDBY_GROUP} ip ${IP_VIRTUAL_DEFAULT_GATEWAY}
  <li>standby ${ID_STANDBY_GROUP} priority ${NUM}
  <li>standby ${ID_STANDBY_GROUP} preemt // Если необходимо указать
  </ul>

<h2 name=11>Настройка VPN</h2>
<h3>Теория</h3>
Virtual Private Network - это технология, которая обеспечивает безопасное и приватное соединение между устройствами через общедоступную сеть, такую как интернет. VPN создает виртуальную частную сеть, позволяющую пользователям обмениваться данными и ресурсами в зашифрованном и защищенном виде, как если бы они находились в одной физической сети.
Generic Routing Encapsulation - это протокол обобщенной инкапсуляции маршрутизации, который используется для создания виртуальных частных сетей (VPN) или установления логических соединений между удаленными сетями через сети общего пользования, такие как Интернет. Основная цель протокола GRE состоит в том, чтобы упаковать и передать сетевые пакеты одного протокола через сеть, предназначенную для другого протокола. Он выполняет инкапсуляцию и декапсуляцию пакетов, добавляя дополнительный заголовок, который позволяет маршрутизаторам передавать пакеты по определенному пути в сети.
<h3>Практика</h3>
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
   Per-VLAN Spanning Tree - это протокол множественного дерева охвата для виртуальных локальных сетей (VLAN), разработанный компанией Cisco. Он является расширением стандартного протокола Spanning Tree Protocol (STP) и позволяет создавать отдельное дерево охвата для каждой VLAN в сети. Основная цель PVST состоит в том, чтобы предотвратить петли в сети и обеспечить безопасность и непрерывность работы сети, устраняя возможные проблемы, связанные с наличием множества VLAN.
   RAPID PVST - более быстрый протокол, который быстро реагирует на изменения в сети.
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
   <li> protect - В режиме protect коммутатор отбрасывает входящий трафик с некорректными MAC-адресами, но не выполняет других действий. То есть некорректные кадры просто отбрасываются, но порт остается активным и продолжает функционировать для других корректных устройств.
   <li> restrict - В режиме restrict коммутатор отбрасывает входящий трафик с некорректными MAC-адресами и записывает информацию о нарушении в системный журнал (лог). Кроме того, уведомления могут быть отправлены администратору с целью предупредить о нарушении, но порт остается активным для остальных корректных устройств.
   <li> shutdown (по умолчанию) - В режиме shutdown (отключение) порт, на котором было обнаружено нарушение, полностью отключается. Это означает, что порт становится неактивным и все трафик на нем останавливается. Для возобновления работы порта администратор должен вручную включить его. 
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
  &ensp;║<br>
  &ensp;Vㅤ 5 6 7 8<br>
  &ensp;A = 1 0 1 0 => 1000 = 8<br>
   ㅤㅤㅤㅤㅤㅤㅤㅤ&ensp;ㅤㅤ║<br>
   ㅤㅤㅤㅤㅤㅤㅤㅤ&ensp;ㅤㅤV<br>
   ㅤㅤㅤㅤㅤㅤㅤㅤ&ensp;ㅤ A8AA:BBFF:FEBB:CCCC<br>
  
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
   </ul>
   <ul><li>Дальше также необходимо написать у интерфейса, какой протокол маршрутизации был использован(симот на протокол EIGRP_v6)</li></ul>
   
   <h2 name=19>Настройка DHCP_v6</h2>
   config:
   <ol>
   <li> ipv6 dhcp pool ${NAME_POOL}
   <ul>
   <li> address prefix ${IPv6_ADDRESS}/${MASK}
   </ul>
   <li> int ${INT}
   <ul>
   <li> ipv6 dhcp server ${NAME_POOL}
   <li> ipv6 nd ${TYPE_DHCP}
   </ul>
   </ol>
   <ul>
   <li> NAME_POOL -> название пула
   <li> IPv6_ADDRESS -> адрес сети
   <li> MASK -> префикс маски
   <li> INT -> интерфейс к которому идет настройка
   <li> TYPE_DHCP:
   <ul>
   <li> managed-config-flag -> DHCP
   <li> ra -> DHCP + SLAAC
   </ul>
   </ul>
   
   <h2 name=20>Настройка NAT-PT</h2>
   config:
   <ul>
   <li> ipv6 nat prefix ${IPv6_ADDRESS}/96
   <li> ipv6 nat ${CONVERT} source ${ADRESS} ${NEW_ADDRESS}
   <li> interface ${INT} 
   <ul>
   <li> ipv6 nat
   </ul>
   </ul>
   <ul>
   <li>IPv6_ADDRESS -> адрес сети версии 6
   <li> CONVERT -> v6v4 или v4v6
   <li> ADDRESS -> адрес источника, который необходимо преобразовать в другой тип версии сети
   <li> NEW_ADDRES -> новый адрес источника, другой версии сети. важно чтобы он не пересекался с другими IP адресами...
   </ul>
   
   <h2 name=21>Редистрибьюция маршрутов</h2>
   <p><b>Теория:</b>Редистрибуция маршрутов (route redistribution) - это процесс передачи маршрутной информации из одного протокола маршрутизации в другой. Она позволяет объединить маршруты из разных протоколов маршрутизации и распространить их по всей сети.</p>
   <br>config-router(OSPF):
   <ul>
   <li>redistribute eigrp ${PROCESS_ID} subnets
   </ul>
   <br>config-router(EIGRP):
   <ul>
   <li>redistribute ospf ${PROCESS_ID} metric 1000 35 255 1 1500
   </ul>
   
   
   <h2 name=22>SPLIT-HORIZON</h2>
   <p>Split Horizon (Разделение горизонта) - это техника, используемая в протоколах дистанционного векторного маршрутизации (Distance Vector Routing Protocols) для предотвращения возникновения маршрутных петель. Маршрутные петли - это ситуации, когда пакет данных продолжает кружить между несколькими маршрутизаторами без возможности достичь своего назначения. Она состоит в том, что маршрутизаторы не отправляют информацию о маршрутах обратно по тому же интерфейсу, через который они получили эту информацию. Это означает, что маршрутизаторы не будут пересылать обновления о маршрутах обратно на тот же маршрутизатор, от которого они получили эти обновления.</p>

   <h2 name=23>Процесс загрузки маршрутизатора</h2>
   <img src="https://github.com/Golfriman/CISCO_helper/blob/main/image.png">
   
   <h2 name=24>BGP</h2>
   BGP (Border Gateway Protocol) - это протокол маршрутизации на основе векторных дистанций, используемый для обмена информацией о маршрутах между автономными системами (AS) в Интернете. Он играет ключевую роль в глобальной маршрутизации и связывает различные сети провайдеров и автономные системы.
Пример: У тебя есть компания, провайдер раздает интернет. Но ты не уверен, что этот провайдер будет всегда стабилен, поэтому ты заключаешь соглашение с еще одним провайдером и добавляешь доступ в интернет с новым провайдером. Итог - два выхода в интернет. Это и есть BGP.
   
   <h2 name=25>Доп инфо</h2>
   <br>BackboneFast - это технология, применяемая в протоколе Spanning Tree Protocol (STP) для быстрого обнаружения и восстановления связности в сетях Ethernet при переходе от активного кабеля на резервный кабель.
   <br>UplinkFast - это технология, применяемая в протоколе Spanning Tree Protocol (STP) для ускорения процесса сходимости сети при отказе активного уплинка (соединения с верхним уровнем сети) и переключении на резервный уплинк.
   <br>Время сходимости протокола Spanning Tree Protocol (STP) - это время, которое требуется для того, чтобы протокол STP совершил переход от состояния блокировки порта к состоянию прохождения трафика через порт после изменения топологии сети или отказа в сети.
   <br>RIPNG (Routing Information Protocol Next Generation) был разработан, чтобы расширить возможности RIP и адаптироваться к сетевым сценариям, использующим IPv6. Он предлагает поддержку IPv6, новые функции и улучшенную безопасность по сравнению с классическим RIP, который предназначен для IPv4.
   <br>BPDU Guard - это функция, используемая в протоколе Spanning Tree Protocol (STP), которая обеспечивает защиту сети от непреднамеренного или нежелательного появления Bridge Protocol Data Units (BPDU) на портах коммутатора, которые не должны получать такие пакеты. BPDU Guard помогает предотвратить возникновение петель и обеспечить безопасность и надежность сети. Он рекомендуется использовать на портах, к которым подключены конечные устройства, такие как компьютеры или принтеры, где не ожидается передача BPDU-пакетов.
   <br>BPDU Filter - это функция, используемая в протоколе Spanning Tree Protocol (STP) для блокировки отправки и приема Bridge Protocol Data Units (BPDU) на определенных портах коммутатора. Когда BPDU Filter включен на порту, коммутатор перестает отправлять и принимать BPDU-пакеты через этот порт. BPDU-пакеты содержат информацию о топологии сети и используются STP для обнаружения петель и управления состоянием портов. Заблокировка BPDU-пакетов на порту с помощью BPDU Filter может привести к потенциальным проблемам, таким как возникновение петель и несогласованность топологии, поэтому необходимо использовать эту функцию с осторожностью.
   <br>DUAL (Diffusing Update Algorithm) - это алгоритм, используемый в протоколе маршрутизации EIGRP (Enhanced Interior Gateway Routing Protocol). DUAL является центральной частью EIGRP и отвечает за выбор и поддержание лучших путей маршрутизации в сети. Основная цель DUAL - обеспечить быструю сходимость сети и оптимальный выбор маршрутов на основе метрик, пропускной способности и задержки. DUAL работает на каждом узле EIGRP и обменивается информацией о маршрутах с соседними узлами. В работе DUAL используется понятие "фактического" и "потенциального" маршрута. Фактический маршрут - это текущий выбранный маршрут, который используется для отправки пакетов. Потенциальный маршрут - это альтернативный маршрут, который может быть выбран, если фактический маршрут станет недоступным или неподходящим. DUAL использует информацию о фактическом и потенциальном маршрутах, а также метрики и связи с соседними узлами для принятия решения о переходе на альтернативный маршрут, если это необходимо. Алгоритм DUAL работает по принципу "жадной" оптимизации, выбирая лучший доступный маршрут на основе определенных критериев. DUAL обеспечивает быструю сходимость сети, минимизирует количество пересылки маршрутной информации и обеспечивает высокую отказоустойчивость. Он способствует эффективной маршрутизации в сети и поддерживает оптимальный выбор путей для доставки данных.
   <br>EIGRP Stub (EIGRP стабильность) - это функция в протоколе маршрутизации EIGRP (Enhanced Interior Gateway Routing Protocol), которая позволяет настроить коммутаторы или маршрутизаторы в сети в качестве "stub" (заглушка) или "недействительных" узлов. Когда узел настроен как EIGRP Stub, он ограничивает свою способность получать и пересылать маршрутную информацию, что приводит к более эффективному использованию ресурсов и повышает стабильность сети.
