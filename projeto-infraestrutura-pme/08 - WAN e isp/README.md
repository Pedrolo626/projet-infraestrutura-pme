# 08 — WAN e Simulação de ISP

`
# 08 — WAN e Simulação de ISP

## Objetivo

Estender a infraestrutura da PME para uma rede externa, simulando a conexão da empresa com um provedor de serviços (ISP).

Nesta etapa, foi criada uma conexão WAN entre o roteador principal da PME (R1) e um segundo roteador (R2), representando o ISP. O R2 também fornece acesso a uma rede externa contendo um servidor.

O objetivo é demonstrar, de forma prática:

- conexão de uma rede corporativa a uma rede externa;
- utilização de uma interface WAN;
- comunicação entre a PME e um ISP;
- roteamento entre redes distintas;
- utilização de uma rota padrão;
- utilização de rotas estáticas;
- necessidade de rotas de retorno;
- comunicação bidirecional entre a rede interna e uma rede externa.

> **Observação:** esta etapa não implementa NAT/PAT. A rede externa é uma simulação controlada dentro do Cisco Packet Tracer, utilizando endereços reservados para documentação e exemplos.

---

## Topologia


                         REDE EXTERNA
                              │
                           EXT-SRV
                       198.51.100.10
                              │
                       198.51.100.1
                              │
                             R2
                            ISP
                              │
                       203.0.113.1
                              │
                           WAN
                              │
                       203.0.113.2
                              │
                             R1
                       Roteador PME
                              │
                             SW1
                           /     \
                         SW2    Servidores
                        /  \
               Financeiro  TI  Vendas
`

### Organização da topologia

* **R1:** roteador principal da PME.
* **R2:** roteador utilizado para representar o ISP.
* **EXT-SRV:** servidor localizado na rede externa à PME.
* **R1 G0/1 ↔ R2 G0/0:** enlace WAN.
* **R2 G0/1 ↔ EXT-SRV:** rede externa.
* A infraestrutura interna da PME permanece conectada ao R1 por meio do enlace trunk e das subinterfaces configuradas anteriormente.

A separação física dos equipamentos no Packet Tracer também representa visualmente três ambientes:


┌───────────────────────┐
│     AMBIENTE PME      │
│ PCs / Switches / R1   │
└───────────┬───────────┘
            │
            │ WAN
            │
┌───────────▼───────────┐
│   ISP / OPERADORA     │
│          R2           │
└───────────┬───────────┘
            │
            │
┌───────────▼───────────┐
│    REDE EXTERNA       │
│       EXT-SRV         │
└───────────────────────┘


---

## Endereçamento

### Enlace WAN — R1 ↔ R2

Rede:

`203.0.113.0/30`

| Dispositivo | Interface | Endereço IP      |
| ----------- | --------- | ---------------- |
| R1          | G0/1      | `203.0.113.2/30` |
| R2          | G0/0      | `203.0.113.1/30` |

A máscara utilizada é:

`255.255.255.252`

A rede possui quatro endereços:

* Rede: `203.0.113.0`
* R2: `203.0.113.1`
* R1: `203.0.113.2`
* Broadcast: `203.0.113.3`

---

### Rede externa

Rede:

`198.51.100.0/24`

| Dispositivo | Interface | Endereço IP        |
| ----------- | --------- | ------------------ |
| R2          | G0/1      | `198.51.100.1/24`  |
| EXT-SRV     | Fa0       | `198.51.100.10/24` |

Gateway do servidor:

`198.51.100.1`

Os endereços `203.0.113.0/24` e `198.51.100.0/24` pertencem às faixas reservadas para documentação e exemplos, sendo adequados para a simulação do laboratório.

---

## Configuração do R1

A interface G0/1 foi utilizada como interface WAN.


R1(config)#interface g0/1
R1(config-if)#ip address 203.0.113.2 255.255.255.252
R1(config-if)#no shutdown


Após a configuração, a interface apresentou:


GigabitEthernet0/1     203.0.113.2    YES manual up    up


### Rota padrão

Foi adicionada uma rota padrão apontando para o R2:


R1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.1


A rota informa ao R1 que, para qualquer rede que não esteja diretamente conectada ou não possua uma rota mais específica, o próximo salto deve ser o R2.

A tabela de roteamento passou a apresentar:


Gateway of last resort is 203.0.113.1 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 203.0.113.1


As redes internas da PME continuam sendo atendidas pelas subinterfaces da G0/0:


192.168.10.0/24 → G0/0.10
192.168.20.0/24 → G0/0.20
192.168.30.0/24 → G0/0.30
192.168.40.0/24 → G0/0.40
192.168.50.0/24 → G0/0.50
192.168.99.0/24 → G0/0.99


---

## Configuração do R2

O R2 representa o ISP.

### Interface voltada para a PME


R2(config)#interface g0/0
R2(config-if)#ip address 203.0.113.1 255.255.255.252
R2(config-if)#no shutdown


Essa interface estabelece o enlace WAN com o R1.

### Interface voltada para a rede externa


R2(config)#interface g0/1
R2(config-if)#ip address 198.51.100.1 255.255.255.0
R2(config-if)#no shutdown


Essa interface funciona como gateway da rede externa.

O R2 ficou com:


GigabitEthernet0/0     203.0.113.1    YES manual up    up
GigabitEthernet0/1     198.51.100.1   YES manual up    up


---

## Configuração do servidor externo

O servidor `EXT-SRV` foi configurado com:


IP Address:       198.51.100.10
Subnet Mask:      255.255.255.0
Default Gateway:  198.51.100.1


O servidor representa um equipamento localizado fora da infraestrutura da PME.

Não foi necessário configurar DNS ou outros serviços para esta etapa. O servidor foi utilizado principalmente como destino para validar o roteamento entre a PME e a rede externa.

---

## Rotas de retorno no R2

Inicialmente, o R2 conhecia somente as redes diretamente conectadas:


203.0.113.0/30 → G0/0
198.51.100.0/24 → G0/1


Com isso, o R1 conseguia alcançar o servidor externo, pois possuía uma rota padrão apontando para o R2.

Entretanto, quando um computador da PME tentou acessar o servidor externo, a comunicação não foi concluída.

O motivo foi que o R2 não possuía rotas para as redes internas da PME.

Foram então adicionadas rotas estáticas:


R2(config)#ip route 192.168.10.0 255.255.255.0 203.0.113.2
R2(config)#ip route 192.168.20.0 255.255.255.0 203.0.113.2
R2(config)#ip route 192.168.30.0 255.255.255.0 203.0.113.2
R2(config)#ip route 192.168.40.0 255.255.255.0 203.0.113.2
R2(config)#ip route 192.168.50.0 255.255.255.0 203.0.113.2
R2(config)#ip route 192.168.99.0 255.255.255.0 203.0.113.2


Essas rotas informam ao R2 que as redes internas da PME podem ser alcançadas através do R1.

---

## Conceito de roteamento demonstrado

Esta etapa demonstrou na prática que uma comunicação entre duas redes precisa possuir um caminho de ida e um caminho de retorno.

### Caminho de ida

Quando um computador da PME acessa o servidor externo:


PC da PME
    ↓
Gateway da VLAN
    ↓
R1
    ↓
R2 / ISP
    ↓
EXT-SRV


O R1 utiliza sua rota padrão:


0.0.0.0/0 → 203.0.113.1


### Caminho de retorno

Quando o servidor responde:


EXT-SRV
    ↓
R2
    ↓
R1
    ↓
Gateway da VLAN
    ↓
PC da PME


O R2 utiliza as rotas estáticas configuradas para as redes `192.168.x.x`.

Esse comportamento permitiu observar por que simplesmente configurar uma rota de saída não é suficiente para garantir uma comunicação bidirecional.

---

## Validação

### 1. R1 → R2

Comando utilizado:


R1#ping 203.0.113.1


**Resultado:** sucesso.

O enlace WAN entre a PME e o ISP foi validado.

---

### 2. R2 → R1

Comando utilizado:


R2#ping 203.0.113.2


**Resultado:** sucesso.

A comunicação no sentido contrário também foi validada.

---

### 3. R2 → servidor externo

Comando utilizado:


R2#ping 198.51.100.10


**Resultado:** sucesso.

A comunicação entre o ISP simulado e a rede externa foi validada.

---

### 4. R1 → servidor externo

Comando utilizado:


R1#ping 198.51.100.10


**Resultado:** sucesso.

O teste confirmou que a rota padrão do R1 estava funcionando.

---

### 5. PC da PME → servidor externo

Um computador da rede interna foi utilizado para testar a comunicação:


PC>ping 198.51.100.10


Inicialmente, todos os pacotes foram perdidos.

Isso ocorreu porque o R2 ainda não possuía rotas para as redes internas da PME.

Após a configuração das rotas estáticas no R2:


PC>ping 198.51.100.10


**Resultado:** sucesso.

---

### 6. Servidor externo → PC da PME

Foi realizado também um teste no sentido inverso:


EXT-SRV>ping 192.168.10.x


utilizando o endereço IP do PC da PME.

**Resultado:** sucesso.

Com isso, foi confirmada a comunicação bidirecional entre a rede interna e a rede externa.

---

## Fluxo final da comunicação

Após todas as configurações:


                     REDE EXTERNA
                          │
                    198.51.100.10
                       EXT-SRV
                          │
                    198.51.100.1
                          │
                         R2
                   ISP / OPERADORA
                          │
                    203.0.113.1
                          │
                   203.0.113.2
                          │
                         R1
                    ROTEADOR PME
                          │
                         SW1
                       /     \
                     SW2   Servidores
                    /  \
             Financeiro TI Vendas


O fluxo de ida:


PC → R1 → R2 → EXT-SRV


E o fluxo de retorno:


EXT-SRV → R2 → R1 → PC


foram validados com sucesso.

---

## NAT/PAT

NAT/PAT não foi implementado nesta etapa.

A decisão foi manter o foco em:

* WAN;
* ISP;
* roteamento;
* rota padrão;
* rotas estáticas;
* caminhos de ida e retorno;
* comunicação entre redes distintas.

A utilização de NAT/PAT exigiria uma etapa específica para trabalhar a tradução de endereços privados e públicos e não é necessária para o objetivo deste laboratório.

---

## Resultado

A infraestrutura da PME passou a possuir conectividade com uma rede externa simulada através de um ISP.

Com a conclusão desta etapa, o projeto passou a representar não apenas a infraestrutura interna da empresa, mas também sua comunicação com uma rede externa.

A infraestrutura desenvolvida até aqui inclui:

* topologia física de uma PME;
* segmentação por VLANs;
* roteamento inter-VLAN;
* DHCP;
* servidores e serviços internos;
* gerenciamento via SSH;
* hardening básico;
* conexão WAN;
* ISP simulado;
* rede externa;
* roteamento estático;
* rota padrão;
* comunicação bidirecional entre a PME e a rede externa.

---

## Baseline

Após a conclusão e validação de todas as configurações, este estado foi salvo como:


wan-isp-baseline.pkt


Arquivo:


08-wan-e-isp/
├── wan-isp-baseline.pkt
└── README.md



