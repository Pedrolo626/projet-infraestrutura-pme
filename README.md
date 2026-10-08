
# — Infraestrutura de Pequena Empresa (PME)

## Visão geral

Este projeto consiste na construção progressiva de uma infraestrutura de rede para uma pequena empresa (PME) utilizando o **Cisco Packet Tracer**.

A proposta é simular, de forma prática e evolutiva, componentes encontrados em uma infraestrutura corporativa de pequeno porte, passando desde a construção da topologia física até segmentação de rede, roteamento, serviços, gerenciamento, hardening, conexão WAN/ISP e troubleshooting.

O projeto foi desenvolvido com foco em **Redes, Infraestrutura e Suporte N1**, buscando representar situações próximas às encontradas em ambientes corporativos reais, sem deixar de manter o escopo adequado para um laboratório acadêmico.

---

## Objetivos

O projeto tem como objetivos:

- construir uma topologia de rede corporativa de pequeno porte;
- aplicar conceitos de switching e VLANs;
- implementar roteamento inter-VLAN;
- configurar DHCP;
- disponibilizar serviços de rede;
- implementar gerenciamento remoto utilizando SSH;
- aplicar medidas básicas de hardening;
- estabelecer uma conexão WAN com um ISP simulado;
- permitir comunicação com uma rede externa;
- desenvolver cenários de troubleshooting;
- praticar diagnóstico baseado em sintomas, testes e evidências.

---

## Arquitetura

A infraestrutura interna da PME é composta por:

- 1 roteador principal (R1);
- 2 switches de acesso (SW1 e SW2);
- 3 servidores;
- 2 impressoras;
- 10 computadores distribuídos entre diferentes setores;
- VLANs para segmentação da rede;
- gerenciamento separado em uma VLAN própria.

A partir da etapa 08, a infraestrutura é expandida para incluir:

- 1 roteador adicional (R2), representando um ISP;
- 1 enlace WAN entre a PME e o ISP;
- 1 rede externa;
- 1 servidor localizado na rede externa simulada.

### Topologia geral


                              REDE EXTERNA
                                   │
                                EXT-SRV
                                   │
                                  R2
                              ISP / WAN
                                   │
                                  R1
                           ROTEADOR DA PME
                                   │
                                  SW1
                                /     \
                              SW2    SERVIDORES
                             /  \
                      Financeiro  TI  Vendas


A topologia é construída progressivamente ao longo das etapas, portanto nem todos os componentes estão presentes ou configurados desde o primeiro estágio.

---

## Segmentação da rede

A rede interna utiliza as seguintes VLANs:

| VLAN | Nome          | Rede              |
| ---: | ------------- | ----------------- |
|   10 | FINANCEIRO    | `192.168.10.0/24` |
|   20 | TI            | `192.168.20.0/24` |
|   30 | VENDAS        | `192.168.30.0/24` |
|   40 | SERVIDORES    | `192.168.40.0/24` |
|   50 | IMPRESSORAS   | `192.168.50.0/24` |
|   99 | GERENCIAMENTO | `192.168.99.0/24` |

A comunicação entre as VLANs é realizada por meio de **Router-on-a-Stick**, utilizando subinterfaces no R1.

---

## Serviços e infraestrutura

Ao longo do projeto foram implementados e validados:

* VLANs;
* trunks;
* roteamento inter-VLAN;
* DHCP;
* DNS;
* HTTP/Intranet;
* servidor de arquivos/backup;
* gerenciamento por SSH;
* gerenciamento por VLAN dedicada;
* autenticação local;
* hardening básico de dispositivos;
* WAN;
* roteamento estático;
* rota padrão;
* comunicação com rede externa.

---

# Etapas do projeto

## 01 — Topologia Base

Construção da topologia física inicial da PME.

Nesta etapa são posicionados os principais dispositivos da infraestrutura:

* R1;
* SW1;
* SW2;
* computadores;
* servidores;
* impressoras.

O objetivo é estabelecer a estrutura física que será utilizada nas etapas seguintes.

**Baseline:** `infraestrutura-pme-baseline.pkt`

---

## 02 — VLANs e Segmentação

Implementação da segmentação lógica da rede por meio de VLANs.

Foram criadas VLANs específicas para:

* Financeiro;
* TI;
* Vendas;
* Servidores;
* Impressoras;
* Gerenciamento.

Também foram configuradas as portas de acesso e os enlaces trunk entre os switches e o R1.

**Baseline:** `vlans-baseline.pkt`

---

## 03 — Roteamento Inter-VLAN

Implementação do roteamento entre as diferentes VLANs utilizando **Router-on-a-Stick**.

O R1 passa a atuar como gateway das redes internas por meio de subinterfaces:


G0/0.10 → 192.168.10.1
G0/0.20 → 192.168.20.1
G0/0.30 → 192.168.30.1
G0/0.40 → 192.168.40.1
G0/0.50 → 192.168.50.1
G0/0.99 → 192.168.99.1


A conectividade entre as diferentes redes foi validada por meio de testes de comunicação.

**Baseline:** `inter-vlan-baseline.pkt`

---

## 04 — DHCP

Implementação do serviço DHCP no R1 para distribuição automática de endereços IP aos computadores das VLANs de usuários.

Foram criados pools específicos para:

* Financeiro;
* TI;
* Vendas.

Além do endereço IP, os clientes recebem informações como:

* máscara de sub-rede;
* gateway padrão;
* servidor DNS.

A obtenção automática de endereços pelos computadores foi validada.

**Baseline:** `dhcp-baseline.pkt`

---

## 05 — Servidores e Serviços

Implementação dos principais serviços internos da PME.

Foram utilizados servidores para:

* DNS;
* Intranet/HTTP;
* arquivos e backup.

O DNS foi configurado para resolução de nomes internos, permitindo acessar serviços utilizando nomes como:


dns.empresa.local
intranet.empresa.local
files.empresa.local


A resolução de nomes e o acesso aos serviços foram validados.

**Baseline:** `servidores-baseline.pkt`

---

## 06 — Gerenciamento

Implementação de uma estrutura básica de gerenciamento remoto dos dispositivos de rede.

Foi utilizada a VLAN 99 como rede de gerenciamento, com endereços destinados aos dispositivos:


R1  → 192.168.99.1
SW1 → 192.168.99.2
SW2 → 192.168.99.3


Também foi configurado acesso remoto utilizando **SSH versão 2**, com autenticação local.

O objetivo é substituir métodos inseguros de gerenciamento remoto por uma alternativa mais adequada para ambientes corporativos.

**Baseline:** `ssh-gerenciamento.pkt`

---

## 07 — Hardening

Aplicação de medidas básicas de segurança nos dispositivos de rede.

Entre as configurações realizadas estão:

* `enable secret`;
* criptografia de senhas;
* banner de acesso restrito;
* autenticação local;
* acesso remoto exclusivamente por SSH;
* SSH versão 2;
* autenticação no console;
* desativação de portas não utilizadas nos switches.

As credenciais utilizadas no laboratório são fictícias e destinadas exclusivamente ao ambiente de estudo.

**Baseline:** `hardening-baseline.pkt`

---

## 08 — WAN e ISP

Expansão da infraestrutura para representar a conexão da PME com uma rede externa através de um ISP simulado.

Foi adicionado o R2, representando a operadora/ISP, e uma rede externa contendo o servidor `EXT-SRV`.

### Enlace WAN


R1 G0/1 → 203.0.113.2/30
R2 G0/0 → 203.0.113.1/30


### Rede externa


R2 G0/1  → 198.51.100.1/24
EXT-SRV  → 198.51.100.10/24


No R1 foi configurada uma rota padrão apontando para o R2.

No R2 foram configuradas rotas estáticas de retorno para as redes internas da PME.

A comunicação foi validada nos dois sentidos:


PME → ISP → Rede externa
Rede externa → ISP → PME


NAT/PAT não foi implementado nesta etapa. O objetivo foi concentrar o laboratório em WAN e roteamento entre redes.

**Baseline:** `wan-isp-baseline.pkt`

---

# 09 — Troubleshooting

A etapa final reúne cenários de falhas propositalmente introduzidas na infraestrutura para praticar diagnóstico e resolução de problemas de rede.

Os incidentes seguem uma abordagem baseada em:


Sintoma
   ↓
Coleta de informações
   ↓
Hipóteses
   ↓
Testes
   ↓
Evidências
   ↓
Identificação da causa
   ↓
Correção
   ↓
Validação


Foram desenvolvidos os seguintes cenários:

### 01 — VLAN incorreta

Simulação de uma porta configurada na VLAN incorreta, impedindo a comunicação esperada do dispositivo.

### 02 — Trunk incorreto

Simulação de uma falha no enlace trunk entre os switches, afetando a propagação das VLANs.

### 03 — Gateway incorreto

Simulação de configuração incorreta do gateway padrão de um dispositivo.

### 04 — DHCP incorreto

Simulação de uma falha relacionada à obtenção automática de configuração IP.

### 05 — DNS incorreto

Simulação de uma falha na resolução de nomes, mantendo a possibilidade de comunicação por endereço IP.

### 06 — SSH incorreto

Simulação de uma falha no acesso remoto por SSH aos dispositivos de rede.

Os arquivos `.pkt` representam ambientes de prática para diagnóstico e são acompanhados de um registro central com os sintomas, testes realizados, causa identificada, correção e validação.

---

## Estrutura do projeto


```text
projeto-infraestrutura-pme/
├── README.md
├── 01-topologia-base/
│   ├── infraestrutura-pme-baseline.pkt
│   └── README.md
├── 02-vlans-e-segmentacao/
│   ├── vlans-baseline.pkt
│   └── README.md
├── 03-roteamento-inter-vlan/
│   ├── inter-vlan-baseline.pkt
│   └── README.md
├── 04-dhcp/
│   ├── dhcp-baseline.pkt
│   └── README.md
├── 05-servidores-e-servicos/
│   ├── servidores-baseline.pkt
│   └── README.md
├── 06-gerenciamento/
│   ├── ssh-gerenciamento.pkt
│   └── README.md
├── 07-hardening/
│   ├── hardening-baseline.pkt
│   └── README.md
├── 08-wan-e-isp/
│   ├── wan-isp-baseline.pkt
│   └── README.md
└── 09-troubleshooting/
    ├── README.md
    ├── 01-vlan-incorreta/
    │   └── falha.pkt
    ├── 02-trunk-incorreto/
    │   └── falha.pkt
    ├── 03-gateway-incorreto/
    │   └── falha.pkt
    ├── 04-dhcp-incorreto/
    │   └── falha.pkt
    ├── 05-dns-incorreto/
    │   └── falha.pkt
    └── 06-ssh-incorreto/
        └── falha.pkt
```
## Resultado

Ao final do projeto, foi construída uma infraestrutura de PME funcional e progressiva, partindo de uma topologia física simples e chegando a um ambiente com:

* segmentação por VLANs;
* roteamento inter-VLAN;
* DHCP;
* DNS;
* servidores internos;
* gerenciamento por SSH;
* hardening básico;
* conexão WAN;
* ISP simulado;
* rede externa;
* roteamento estático;
* rota padrão;
* troubleshooting de falhas de rede.

O projeto foi desenvolvido de forma incremental, mantendo um **baseline funcional ao final de cada etapa**, permitindo acompanhar a evolução da infraestrutura e utilizar os estados anteriores como referência para as etapas seguintes.



