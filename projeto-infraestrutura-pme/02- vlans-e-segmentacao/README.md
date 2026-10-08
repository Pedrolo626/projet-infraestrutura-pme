Etapa 02 — VLANs e Segmentação

Objetivo

Esta etapa implementa a segmentação lógica da rede por meio de VLANs, separando os diferentes setores e tipos de dispositivos da pequena empresa em diferentes domínios de broadcast.

Também são configuradas as portas de acesso dos switches e os enlaces trunk necessários para transportar múltiplas VLANs entre os equipamentos.

O roteamento entre as VLANs ainda não é implementado nesta etapa. Essa função será realizada posteriormente por meio de roteamento inter-VLAN.

VLANs

A rede foi dividida nas seguintes VLANs:

VLAN	Nome	Rede planejada	Finalidade
10	FINANCEIRO	192.168.10.0/24	Estações do setor Financeiro
20	TI	192.168.20.0/24	Estações do setor de TI
30	VENDAS	192.168.30.0/24	Estações do setor de Vendas
40	SERVIDORES	192.168.40.0/24	Servidores da empresa
50	IMPRESSORAS	192.168.50.0/24	Impressoras de rede
99	GERENCIAMENTO	192.168.99.0/24	Gerenciamento dos equipamentos de rede

Observação: as redes IP acima foram definidas como parte do planejamento da infraestrutura, mas o endereçamento IP das VLANs ainda não foi configurado nesta etapa.


Conceitos implementados

VLAN

Uma VLAN permite dividir logicamente uma mesma infraestrutura física em diferentes redes de camada 2.

Neste projeto, cada setor possui sua própria VLAN, reduzindo o domínio de broadcast e estabelecendo uma separação lógica entre os grupos de dispositivos.

Portas de acesso

As portas conectadas aos dispositivos finais foram configuradas como access ports.

Cada porta de acesso pertence a uma única VLAN.


Trunk

Os enlaces entre equipamentos de infraestrutura foram configurados como trunks, permitindo o transporte de múltiplas VLANs pelo mesmo enlace físico utilizando marcação IEEE 802.1Q posterior.

Configuração do SW1
Servidores

As portas destinadas aos servidores foram associadas à VLAN 40:

Fa0/2 → VLAN 40 — DNS
Fa0/3 → VLAN 40 — Intranet/HTTP
Fa0/4 → VLAN 40 — File/Backup
Enlace com SW2
Fa0/24 → Trunk

O enlace entre SW1 e SW2 transporta as VLANs configuradas na infraestrutura.

Enlace com R1
Fa0/1 → Trunk

Esse enlace será utilizado posteriormente para o roteamento inter-VLAN.

No lado do roteador, a interface física permanece como uma interface de camada 3. O funcionamento com múltiplas VLANs será implementado posteriormente por meio de subinterfaces e encapsulamento 802.1Q.

Configuração do SW2
Impressoras
Fa0/2 → VLAN 50 — Impressora 1
Fa0/3 → VLAN 50 — Impressora 2

Financeiro
Fa0/4 → VLAN 10 — PC0
Fa0/5 → VLAN 10 — PC1
Fa0/6 → VLAN 10 — PC2

TI
Fa0/7 → VLAN 20 — PC3
Fa0/8 → VLAN 20 — PC4
Fa0/9 → VLAN 20 — PC5

Vendas
Fa0/10 → VLAN 30 — PC6
Fa0/11 → VLAN 30 — PC7
Fa0/12 → VLAN 30 — PC8
Fa0/13 → VLAN 30 — PC9

Enlace com SW1
Fa0/24 → Trunk


Topologia lógica
                         R1
                          │
                     Fa0/1 │
                       [TRUNK]
                          │
                         SW1
                    ┌─────┴─────┐
                    │           │
               Fa0/24       Servidores
                    │          VLAN 40
                 [TRUNK]
                    │
                   SW2
        ┌───────────┼────────────┐
        │           │            │
     VLAN 10     VLAN 20      VLAN 30
   Financeiro       TI         Vendas
    3 PCs          3 PCs        4 PCs

                VLAN 50
              Impressoras
                 2

A VLAN 99 foi criada para gerenciamento, mas ainda não possui endereçamento ou interfaces de gerenciamento configuradas.

Validação
VLANs

Foi utilizado:

show vlan brief

para verificar a criação das VLANs e a associação das portas de acesso.

O resultado confirmou:

VLAN 10 — Financeiro
VLAN 20 — TI
VLAN 30 — Vendas
VLAN 40 — Servidores
VLAN 50 — Impressoras
VLAN 99 — Gerenciamento
Trunks

Foi utilizado:

show interfaces trunk

para verificar os enlaces trunk.

Os seguintes enlaces foram validados:

SW1 Fa0/24 ↔ SW2 Fa0/24
SW1 Fa0/1  ↔ R1 G0/0

Ambos os enlaces do lado dos switches apresentaram estado de trunking.

Interface do R1

Foi utilizado:

show ip interface brief

A interface:

G0/0

foi ativada e apresentou estado operacional up/up.

Estado da etapa

A segmentação lógica da infraestrutura foi implementada com sucesso.

A rede agora possui diferentes domínios de broadcast para:

Financeiro
TI
Vendas
Servidores
Impressoras
Gerenciamento

Entretanto, as VLANs ainda permanecem isoladas entre si no nível de camada 3.

Nesta etapa ainda não foram configurados:

Endereços IP das VLANs
Subinterfaces no R1
Roteamento inter-VLAN
DHCP
DNS
SSH
Gerenciamento dos switches
Hardening

Esses recursos serão implementados nas etapas seguintes.

Baseline

Arquivo:

vlans-baseline.pkt

Este arquivo representa o estado oficial da infraestrutura após a implementação das VLANs e da segmentação da rede.