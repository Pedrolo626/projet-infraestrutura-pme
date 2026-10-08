# Etapa 04 — DHCP

## Objetivo

Implementar o serviço DHCP no roteador R1 para automatizar a configuração de endereçamento IP dos computadores das VLANs de usuários.

Nesta etapa, os computadores das áreas **Financeiro, TI e Vendas** deixam de utilizar endereços IP configurados manualmente e passam a receber automaticamente:

* Endereço IP
* Máscara de sub-rede
* Gateway padrão
* Servidor DNS

Os servidores e as impressoras permanecem com endereçamento estático.

---

## Conceitos trabalhados

* DHCP (Dynamic Host Configuration Protocol)
* Processo DORA:

  * Discover
  * Offer
  * Request
  * ACK
* Pools DHCP
* Exclusão de endereços
* Gateway padrão
* Distribuição de DNS via DHCP
* DHCP em redes segmentadas por VLAN
* Roteamento inter-VLAN
* Validação de leases DHCP através do Cisco IOS

---

## Arquitetura

O roteador **R1** atua simultaneamente como:

* Gateway das VLANs
* Roteador entre VLANs
* Servidor DHCP

Como o R1 possui uma subinterface diretamente conectada a cada uma das VLANs de usuários, não é necessário utilizar `ip helper-address`.

### VLANs com DHCP

| VLAN | Nome       | Rede            | Gateway      | Faixa dinâmica    |
| ---: | ---------- | --------------- | ------------ | ----------------- |
|   10 | FINANCEIRO | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.21–254 |
|   20 | TI         | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.21–254 |
|   30 | VENDAS     | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.21–254 |

A VLAN 99 — **GERENCIAMENTO** — ainda não possui DHCP nesta etapa.

---

## Endereços excluídos

Os primeiros endereços de cada rede foram reservados para possíveis dispositivos com configuração estática:


ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.20.1 192.168.20.20
ip dhcp excluded-address 192.168.30.1 192.168.30.20


Dessa forma, o DHCP começa a distribuir endereços a partir do `.21`.

Essa estratégia também deixa uma faixa reservada para futuras necessidades de infraestrutura.

---

## Configuração dos pools DHCP

### Financeiro


ip dhcp pool FINANCEIRO
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.40.10


### TI


ip dhcp pool TI
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 192.168.40.10


### Vendas


ip dhcp pool VENDAS
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.40.10


O endereço `192.168.40.10` corresponde ao servidor DNS da rede, localizado na VLAN 40 — SERVIDORES.

---

## Dispositivos com endereçamento estático

Os dispositivos de infraestrutura e serviços não utilizam DHCP.

| Dispositivo | VLAN | IP            |
| ----------- | ---: | ------------- |
| DNS         |   40 | 192.168.40.10 |
| Intranet    |   40 | 192.168.40.11 |
| File/Backup |   40 | 192.168.40.12 |
| Printer1    |   50 | 192.168.50.10 |
| Printer2    |   50 | 192.168.50.11 |

Esses endereços estão dentro das faixas excluídas ou pertencem a VLANs que ainda não possuem pool DHCP.

---

## Validação

Após configurar o DHCP, os computadores das três VLANs foram configurados para obter endereço automaticamente.

### Leases registrados no R1

Comando utilizado:


show ip dhcp binding


Resultado validado:


192.168.10.21    0001.C757.2B58    Automatic
192.168.10.22    0007.EC31.8CC8    Automatic
192.168.10.23    00D0.BAC0.8411    Automatic

192.168.20.21    0050.0F06.5C17    Automatic
192.168.20.22    0001.97CD.2345    Automatic
192.168.20.23    00D0.5854.34CE    Automatic

192.168.30.21    00E0.F7DD.3C22    Automatic
192.168.30.22    00D0.D34D.75A3    Automatic
192.168.30.23    00E0.8F5D.1AEC    Automatic
192.168.30.24    0060.70B4.21DC    Automatic


### Resultado

Foram registrados **10 leases DHCP**, correspondentes aos 10 computadores da rede:

* 3 computadores do Financeiro
* 3 computadores de TI
* 4 computadores de Vendas

Todos receberam endereços pertencentes às suas respectivas sub-redes.

---

## Estado da rede após esta etapa

A infraestrutura possui agora:

* Topologia física da PME
* VLANs configuradas
* Segmentação lógica por departamentos
* Trunks entre os switches e entre SW1/R1
* Roteamento inter-VLAN através de Router-on-a-Stick
* Gateway configurado para cada VLAN
* DHCP centralizado no R1
* Endereçamento automático dos 10 computadores
* Endereçamento estático para servidores e impressoras

Ainda não foram configurados:

* Serviços DNS funcionais
* Servidor Web/Intranet
* Servidor File/Backup
* SSH para gerenciamento
* Gerenciamento da infraestrutura pela VLAN 99
* Hardening adicional
* Conectividade com a rede simulada do ISP
* Incidentes de troubleshooting específicos desta infraestrutura

---

## Baseline

Arquivo salvo como:


04-dhcp/dhcp-baseline.pkt


Este arquivo representa o estado funcional da rede após a implementação e validação do DHCP.

---

## Próxima etapa

### Etapa 05 — Servidores e Serviços

Na próxima etapa serão configurados e validados os serviços dos três servidores da rede:

* **DNS Server** — resolução de nomes internos
* **Intranet Server** — serviço HTTP para simulação de sistema interno da empresa
* **File/Backup Server** — simulação de servidor destinado a arquivos e backup

A etapa também permitirá testar a comunicação entre os computadores das VLANs de usuários e os serviços hospedados na VLAN 40 — SERVIDORES.
