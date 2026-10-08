Etapa 01 — Topologia Base

Objetivo

Esta etapa estabelece a topologia física inicial da infraestrutura de uma pequena empresa (PME), que servirá como base para as configurações e evoluções realizadas nas etapas seguintes do projeto.

O objetivo neste momento é construir e validar a estrutura física da rede, sem implementar ainda mecanismos de segmentação, endereçamento dinâmico, gerenciamento ou hardening.

Infraestrutura

A topologia é composta por:

Equipamento	     Quantidade	   Função
Cisco 2911	         2	         R1 — roteador principal e R2 — ISP/Internet simulada
Cisco 2960	         2	         SW1 — switch principal e SW2 — switch de acesso
Servidores	         3	         DNS, Intranet/HTTP e File/Backup
Impressoras	         2         	Dispositivos de impressão em rede
PCs	               10        	Estações dos usuários

Distribuição dos computadores
Financeiro: 3 PCs
TI: 3 PCs
Vendas: 4 PCs
Topologia
                         INTERNET
                            │
                         [ R2/ISP ]
                            │
                           [ R1 ]
                            │
                          [ SW1 ]
                         /       \
                        /         \
                     [ SW2 ]    Servidores
                        │
              ┌─────────┼─────────┐
              │         │         │
          Financeiro    TI       Vendas
           3 PCs       3 PCs     4 PCs
                        
                     Impressoras

Principais conexões
Roteadores
R2 G0/0 ─── R1 G0/1
Roteador principal e switch principal
R1 G0/0 ─── SW1 Fa0/1

Switches
SW1 Fa0/24 ─── SW2 Fa0/24
Servidores
SW1 Fa0/2 ─── DNS
SW1 Fa0/3 ─── Intranet
SW1 Fa0/4 ─── File/Backup

Impressoras
SW2 Fa0/2 ─── Impressora 1
SW2 Fa0/3 ─── Impressora 2

Estações
SW2 Fa0/4  ─── Financeiro PC0
SW2 Fa0/5  ─── Financeiro PC1
SW2 Fa0/6  ─── Financeiro PC2

SW2 Fa0/7  ─── TI PC3
SW2 Fa0/8  ─── TI PC4
SW2 Fa0/9  ─── TI PC5

SW2 Fa0/10 ─── Vendas PC6
SW2 Fa0/11 ─── Vendas PC7
SW2 Fa0/12 ─── Vendas PC8
SW2 Fa0/13 ─── Vendas PC9

Estado da etapa

A infraestrutura física foi montada e os enlaces entre os switches e dispositivos finais foram validados.

Os enlaces entre os roteadores e entre o R1 e o SW1 permanecem sem configuração lógica nesta etapa. As interfaces dos roteadores ainda não foram ativadas com no shutdown, pois a configuração lógica será realizada nas etapas posteriores.

Nenhuma VLAN, endereço IP, DHCP, roteamento inter-VLAN, SSH ou mecanismo de hardening foi configurado nesta etapa.