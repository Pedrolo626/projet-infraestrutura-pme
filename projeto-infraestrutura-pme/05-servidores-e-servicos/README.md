# Etapa 05 — Servidores e Serviços

## Objetivo

Implementar e validar os principais serviços internos da infraestrutura da PME, utilizando a VLAN 40 — SERVIDORES.

Nesta etapa foram configurados três servidores com endereçamento IP estático:

* Servidor DNS
* Servidor de Intranet/HTTP
* Servidor File/Backup

O objetivo foi simular uma infraestrutura interna básica de servidores e validar seu acesso a partir das diferentes VLANs de usuários.

---

## Conceitos trabalhados

* Servidores com IP estático
* DNS (Domain Name System)
* Registros DNS do tipo A
* Resolução de nomes internos
* HTTP
* Comunicação entre VLANs
* Router-on-a-Stick
* Gateway padrão
* Integração entre DHCP, DNS, roteamento e serviços
* Validação de serviços de rede

---

## VLAN de Servidores

Todos os servidores estão conectados à:


VLAN 40 — SERVIDORES
Rede: 192.168.40.0/24
Gateway: 192.168.40.1


O endereço `192.168.40.1` corresponde à subinterface `G0/0.40` do R1.

---

## Servidores

| Servidor    | Função                       | IP            | Gateway      |
| ----------- | ---------------------------- | ------------- | ------------ |
| DNS         | Resolução de nomes           | 192.168.40.10 | 192.168.40.1 |
| Intranet    | Serviço HTTP                 | 192.168.40.11 | 192.168.40.1 |
| File/Backup | Simulação de arquivos/backup | 192.168.40.12 | 192.168.40.1 |

Os três servidores utilizam endereçamento estático, pois são dispositivos de infraestrutura que precisam manter endereços previsíveis.

---

## Servidor DNS

O serviço DNS foi ativado no servidor `192.168.40.10`.

Foram criados registros internos do tipo **A**:

| Nome                     | Endereço        |
| ------------------------ | --------------- |
| `dns.empresa.local`      | `192.168.40.10` |
| `intranet.empresa.local` | `192.168.40.11` |
| `files.empresa.local`    | `192.168.40.12` |

O endereço do servidor DNS também é distribuído automaticamente aos computadores através das configurações DHCP implementadas na etapa anterior:


DNS Server: 192.168.40.10


Dessa forma, os clientes podem acessar os serviços utilizando nomes em vez de depender diretamente dos endereços IP.

---

## Servidor de Intranet

O servidor `192.168.40.11` foi configurado para disponibilizar um serviço HTTP interno.

O serviço HTTP foi ativado no Packet Tracer e utilizado para simular uma página de Intranet corporativa.

O acesso pode ser realizado através do endereço IP:


http://192.168.40.11


ou através do nome configurado no DNS:


http://intranet.empresa.local


---

## Servidor File/Backup

O servidor `192.168.40.12` representa um servidor interno destinado a arquivos e backup.

Nesta etapa, o foco está no seu posicionamento na VLAN de servidores, endereçamento estático e disponibilidade na rede.

O Packet Tracer não reproduz integralmente uma solução corporativa real de armazenamento e backup, portanto o dispositivo é utilizado como uma representação funcional desse papel dentro do laboratório.

Seu registro DNS é:


files.empresa.local


---

## Validação de conectividade

Os testes foram realizados a partir de computadores das VLANs de usuários.

### Teste por endereço IP

Foram realizados testes de conectividade com:


ping 192.168.40.10
ping 192.168.40.11
ping 192.168.40.12


Todos os testes foram concluídos com sucesso.

Isso confirmou a comunicação entre as VLANs de usuários e a VLAN 40 — SERVIDORES.

---

## Validação de DNS

Foram realizados testes utilizando os nomes configurados no servidor DNS:


ping dns.empresa.local
ping intranet.empresa.local
ping files.empresa.local


Os nomes foram corretamente resolvidos para seus respectivos endereços IP.

Isso confirmou o funcionamento do serviço DNS e sua integração com a configuração DHCP dos clientes.

---

## Validação do serviço HTTP

O acesso à Intranet foi testado através do navegador dos computadores clientes.

Teste por IP:


http://192.168.40.11


Teste por nome:


http://intranet.empresa.local


Ambos os acessos foram concluídos com sucesso.

O segundo teste é especialmente relevante porque valida conjuntamente:


Cliente
   ↓
DNS
   ↓
Resolução de nome
   ↓
192.168.40.11
   ↓
Roteamento inter-VLAN
   ↓
Servidor HTTP


---

## Resultado

Ao final desta etapa, a infraestrutura possui:

* VLAN 40 dedicada aos servidores
* Servidor DNS funcional
* Resolução de nomes internos
* Servidor HTTP funcional
* Intranet acessível pelos clientes
* Servidor File/Backup posicionado na rede de servidores
* Endereçamento estático para os servidores
* Integração entre DHCP, DNS e serviços
* Comunicação validada entre VLANs

A infraestrutura já apresenta uma separação funcional entre:


VLAN 10 → Financeiro
VLAN 20 → TI
VLAN 30 → Vendas
VLAN 40 → Servidores
VLAN 50 → Impressoras
VLAN 99 → Gerenciamento


---

## Estado da rede

Até o momento foram implementados:

* Topologia física
* VLANs e segmentação
* Trunks
* Router-on-a-Stick
* Roteamento inter-VLAN
* DHCP
* DNS
* HTTP/Intranet
* Servidores com IP estático

Ainda não foram implementados:

* Gerenciamento SSH
* Endereçamento de gerenciamento nos switches
* Hardening adicional
* Configuração da rede simulada do ISP
* Incidentes específicos de troubleshooting

---

## Baseline

Arquivo salvo como:


05-servidores-e-servicos/servidores-baseline.pkt


Este arquivo representa o estado funcional da infraestrutura após a implementação e validação dos serviços internos.

---

## Próxima etapa

### Etapa 06 — Gerenciamento por SSH

A próxima etapa será dedicada ao gerenciamento seguro dos dispositivos de infraestrutura.

Serão configurados:

* VLAN 99 — GERENCIAMENTO
* Endereço de gerenciamento do SW1
* Endereço de gerenciamento do SW2
* Acesso remoto via SSH
* Hostname dos dispositivos
* Domínio para geração das chaves RSA
* Usuário local para autenticação
* VTY utilizando SSH
* SSH versão 2

O objetivo será permitir o gerenciamento remoto dos equipamentos sem depender de acesso físico ao console.
