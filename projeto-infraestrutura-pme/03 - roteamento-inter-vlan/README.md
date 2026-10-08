# Etapa 03 — Roteamento Inter-VLAN

## Objetivo

Esta etapa implementa o **roteamento entre as VLANs** da infraestrutura da PME.

Após a segmentação realizada na etapa anterior, cada VLAN funcionava como um domínio de broadcast independente. Nesta etapa, o roteador R1 passou a atuar como gateway das VLANs e a realizar o roteamento entre suas respectivas redes.

Foi utilizado o modelo **Router-on-a-Stick**, utilizando subinterfaces na interface GigabitEthernet0/0 do R1 e encapsulamento IEEE 802.1Q.

---

## Conceito

Cada VLAN possui sua própria rede IP e seu próprio gateway no R1.

| VLAN | Nome          | Rede            | Gateway      |
| ---- | ------------- | --------------- | ------------ |
| 10   | FINANCEIRO    | 192.168.10.0/24 | 192.168.10.1 |
| 20   | TI            | 192.168.20.0/24 | 192.168.20.1 |
| 30   | VENDAS        | 192.168.30.0/24 | 192.168.30.1 |
| 40   | SERVIDORES    | 192.168.40.0/24 | 192.168.40.1 |
| 50   | IMPRESSORAS   | 192.168.50.0/24 | 192.168.50.1 |
| 99   | GERENCIAMENTO | 192.168.99.0/24 | 192.168.99.1 |

O R1 utiliza uma única interface física para atender às diferentes VLANs:


R1 G0/0
├── G0/0.10 → VLAN 10
├── G0/0.20 → VLAN 20
├── G0/0.30 → VLAN 30
├── G0/0.40 → VLAN 40
├── G0/0.50 → VLAN 50
└── G0/0.99 → VLAN 99


O enlace entre R1 e SW1 permanece configurado como trunk no lado do switch, permitindo o transporte das diferentes VLANs até as subinterfaces do roteador.

---

## Configuração do R1

As subinterfaces foram configuradas com encapsulamento IEEE 802.1Q e endereços IP correspondentes às redes de cada VLAN.

### VLAN 10 — Financeiro


interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0


### VLAN 20 — TI


interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0


### VLAN 30 — Vendas


interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0


### VLAN 40 — Servidores


interface g0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0


### VLAN 50 — Impressoras


interface g0/0.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0


### VLAN 99 — Gerenciamento


interface g0/0.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0


---

## Validação

### Estado das interfaces

Foi utilizado:


show ip interface brief


As seis subinterfaces apresentaram estado:


up/up


O resultado confirmou que as interfaces lógicas estavam operacionalmente ativas.

### Tabela de roteamento

Foi utilizado:


show ip route


O R1 passou a apresentar as seis redes como diretamente conectadas:


C 192.168.10.0/24
C 192.168.20.0/24
C 192.168.30.0/24
C 192.168.40.0/24
C 192.168.50.0/24
C 192.168.99.0/24


A letra `C` representa uma rede **Connected**, ou seja, diretamente conectada ao roteador.

---

## Testes de conectividade

Foram utilizados endereços IP estáticos temporários para validar o funcionamento do roteamento antes da implementação do DHCP.

### Endereços utilizados nos testes

| Dispositivo  | VLAN | Endereço IP   |
| ------------ | ---: | ------------- |
| PC0          |   10 | 192.168.10.10 |
| PC3          |   20 | 192.168.20.10 |
| PC6          |   30 | 192.168.30.10 |
| Servidor DNS |   40 | 192.168.40.10 |
| Impressora 1 |   50 | 192.168.50.10 |

### Resultados

Foram realizados testes entre diferentes redes, incluindo:


PC0 → Gateway VLAN 10
PC0 → Gateway VLAN 40
PC0 → Servidor DNS VLAN 40
Servidor DNS → PC0
PC0 → PC3
PC0 → PC6
PC0 → Impressora 1


Todos os testes apresentaram respostas bem-sucedidas.

Isso confirmou a comunicação entre:


VLAN 10 ↔ VLAN 20
VLAN 10 ↔ VLAN 30
VLAN 10 ↔ VLAN 40
VLAN 10 ↔ VLAN 50


e confirmou o funcionamento bidirecional do roteamento entre as redes testadas.

---

## Fluxo de comunicação

Um exemplo de comunicação entre o Financeiro e o servidor:


PC0
192.168.10.10
     │
     │ VLAN 10
     ▼
   SW2
     │
     │ Trunk
     ▼
   SW1
     │
     │ Trunk
     ▼
 R1 G0/0.10
     │
     │ Roteamento
     ▼
 R1 G0/0.40
     │
     │ VLAN 40
     ▼
Servidor
192.168.40.10


O R1 recebe o tráfego destinado à rede `192.168.40.0/24` através da subinterface da VLAN 10, consulta sua tabela de roteamento e encaminha o pacote pela subinterface correspondente à VLAN 40.

---

## Estado da etapa

O roteamento inter-VLAN foi implementado e validado com sucesso.

A infraestrutura agora possui:

* VLANs segmentadas;
* enlaces trunk;
* subinterfaces no R1;
* gateways para as VLANs;
* roteamento entre as redes;
* comunicação validada entre diferentes segmentos.

Ainda não foram implementados:

* DHCP;
* DNS funcional;
* gerenciamento por SSH;
* endereçamento de gerenciamento da VLAN 99;
* hardening;
* conexão com a rede ISP/Internet simulada;
* cenários de troubleshooting.

---

## Baseline

Arquivo:


inter-vlan-baseline.pkt


Este arquivo representa o **estado oficial da infraestrutura após a implementação e validação do roteamento inter-VLAN**.

O baseline deve ser preservado como ponto de referência antes da implementação das próximas funcionalidades.

---

## Próxima etapa

**Etapa 04 — DHCP**

A próxima etapa terá como objetivo automatizar o endereçamento das estações de trabalho.

O R1 será configurado como servidor DHCP para as VLANs de usuários, enquanto servidores e impressoras continuarão utilizando endereçamento estático.

Isso permitirá substituir a configuração manual dos computadores por uma infraestrutura de endereçamento automático mais próxima de um ambiente real de PME.
