# cisco
Адресация 
PC
PC A 192.168.0.11 255.255.255.248
BC B 192.168.0.19 255.255.255.248
PC C 192.168.0.29 255.255.255.248
Router

R0

g0/0 192.168.0.48

g0/1.10 192.168.0.9 255.255.255.248
g0/1.14 192.168.0.17 255.255.255.248

R1

g0/0 192.168.0.57 255.255.255.248

g0/2.10 192.168.0.10 255.255.255.248
g0/2.14 192.168.0.18 255.255.255.248

R2
g0/0 192.168.0.54 255.255.255.248
g0/1 192.168.0.62 255.255.255.248
g0/2 192.168.0.27 255.255.255.248

Задание 1 - базовая конфигурация R2:

Присвоить имя устройству

ena
conf t
hostname Zachet

Отключить поиск DNS 

no ip domain-lookup

Пароль для консольного подключения 
ena
conf t
line con 0
enable seecret console
login

Пароль для удалённого подключения 

line vty 0 15
password Telnet
login

Зашифровать все пароли в файле конфигурации 
service password encryption 

Создать баннермод с текстом Warning
Banner motd #Warning#




Задание 2 - настройка коммутатора S0

ena
conf t


line con 0 
enable seecret console
login

line vty 0 15
password Telnet
login locale

Отключить неиспользуемые порты 
ena 
conf t
int range f0/3-9,f0/12-24 ,g0/1-2
shutdown
Настройка портов  с оконечными устройствами в режим доступа
int f0/10
sw mode access
sw access vlan 10
int f0/11
sw mode access
sw access vlan 14

Настроить Vlan 14 в качестве vlan управления

ena 
conf t
int vlan 14
ip address 192.168.0.20 255.255.255.248
name Managment
no shutdown

настройка подключения из удалённой сети

int f0/11
sw access vlan 14
no shut
username cisko secret cisko

enable seecret console (если не задано на предыдущем шаге)

line vty 0 15
login locale - после этого шага доступен Telnet 

ip domain-name mireauniversity.ru
crypto key generate rsa 

выбираем 1024/512 

ip ssh ver 2

line vty 0 15
transport input ssh  - после этого шага будет доступен ssh
или
transport input telnet 

Настройка порт секурите( не курите! не надо)

int f0/10
sw port-securuty 
sw port-securuty maximum 5
sw port-securuty mac-address sticy (не знаю нужно ли)
sw port-securuty violation protect



Задание 3 - настрока Vlan на S0

ena
conf t
int vlan 14
ip address 192.168.0.20 255.255.255.248
no shut
int vlan 10
no shut
int vlan 80
no shut

int range f0/1-2
sw mode trunk
sw trunk allowed vlan 10,14
sw trunk native vlan 80
end
Задание 4 

R0
int g0/1
no ip address
no shut

int g0/1.10
encaps dot1q 10
ip address 192.168.0.9 255.255.255.248
no shut
int g0/1.14
encaps dot1q 14
ip address 192.168.0.17

R1 
int g0/2
no ip address 
no shut
int g0/2.10
encaps dot1q 10
ip address 192.168.0.10 255.255.255.248
no shut
int g0/1.14
encaps dot1q 14
ip address 192.168.0.18 255.255.255.248
no shut

Задание 5 - создаём статические маршруты 

R0
ena
conf t
ip route 192.168.0.29 255.255.255.255 192.168.0.54(стандартный маршрут)

R1
ena
conf t
ip route 192.168.0.29 255.255.255.255 192.168.0.62(стандартный маршрут)

R2
ena 
conf t
ip route 0.0.0.0 0.0.0.0 192.168.0.49( дефолтный)
ip route 0.0.0.0 0.0.0.0 192.168.0.57 11(плавающий дефолтный с административным расстоянием)

Задание 6

R0
ena
conf t
int g0/1
standby ver 2
int g0/1.10
standby 10 ip 192.168.14
standby 10 priority 150
standby 10 preempt

int g0/1.14
standby 14 ip 192.168.0.22
standby 14 priority 150
standby 14 preempt

R1 
ena 
conf t
int g0/2
standy ver 2
int g0/2.10

standby 10 ip 192.168.14
standby 10 priority 100
int g0/2.14
standby 14 ip 192.168.0.22
standby 14 priority 100

После настройки стендбаев на пк в дефолтном пути указываем:
Для пк в 14 влане DG 192.168.0.22
Для пк в 10 влане DG 192.168.0.14
