# **Etapa 08 — Troubleshooting**

## **Objetivo**

*Os cenários de troubleshooting foram construídos sobre a infraestrutura interna da PME e não dependem da conectividade WAN/ISP introduzida posteriormente na Etapa 08.*

Desenvolver uma metodologia estruturada para diagnóstico e resolução de problemas de rede em uma infraestrutura de PME.

Os incidentes serão reproduzidos no Cisco Packet Tracer a partir do baseline funcional da Etapa 07.

O objetivo não é apenas identificar configurações incorretas, mas desenvolver um processo de investigação baseado em:

**Sintoma → Coleta → Hipóteses → Testes → Eliminação de hipóteses → Causa raiz → Correção → Validação**

---

# **Metodologia geral**

Para qualquer incidente, seguir esta sequência:

### **1. Identificar o sintoma**

Determinar exatamente:

* Quem está afetado?
* O que não funciona?
* O que continua funcionando?
* Quando o problema ocorre?
* O problema afeta um dispositivo ou vários?

### **2. Delimitar o escopo**

Determinar se o problema está relacionado a:

* camada física;
* configuração IP;
* VLAN;
* trunk;
* roteamento;
* DHCP;
* DNS;
* gerenciamento;
* serviço específico.

### **3. Coletar informações**

Começar pelos dispositivos diretamente relacionados ao problema.

Exemplos:


ipconfig
ping
show vlan brief
show interfaces status
show interfaces switchport
show interfaces trunk
show ip interface brief
show ip route
show running-config
show ip ssh


### **4. Formular hipóteses**

Não alterar configurações antes de possuir uma hipótese razoável.

Cada hipótese deve possuir pelo menos um teste capaz de confirmá-la ou eliminá-la.

### **5. Testar**

Executar os testes de maneira progressiva, começando pelos mais simples e menos invasivos.

### **6. Determinar a causa raiz**

A causa deve ser sustentada pelas evidências coletadas.

### **7. Corrigir**

Alterar somente aquilo que foi identificado como causa do problema.

### **8. Validar**

Repetir os testes que originalmente apresentaram falha e realizar testes complementares.

---

# **Incidente 01 — VLAN incorreta**

## **Cenário**

Um computador do Financeiro não consegue acessar recursos da rede.

O PC possui conectividade física e está ligado normalmente.

O objetivo é investigar se o problema está relacionado à associação do dispositivo com a VLAN correta.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/01-vlan-incorreta/falha.pkt


## **Alteração proposital**

No **SW2**, alterar a porta do PC0:


Fa0/4


da:


VLAN 10 — FINANCEIRO


para:


VLAN 20 — TI


Exemplo:


interface fa0/4
 switchport mode access
 switchport access vlan 20


## **Sintoma esperado**

O PC0 continua fisicamente conectado, mas passa a estar em uma rede lógica diferente daquela correspondente ao seu setor.

## **Diagnóstico esperado**

Investigar:


ipconfig
ping gateway
show vlan brief
show interfaces fa0/4 switchport


Hipóteses possíveis:

* IP incorreto;
* gateway incorreto;
* porta na VLAN incorreta;
* problema no gateway;
* problema no trunk.

## **Causa raiz**

Fa0/4 associada à VLAN incorreta.

## **Correção**

Restaurar:


interface fa0/4
 switchport access vlan 10


## **Validação**

Confirmar a VLAN da porta e testar:


ping 192.168.10.1


Além de testar acesso a recursos de outras VLANs.

---

# **Incidente 02 — Trunk incorreto**

## **Cenário**

Usuários conectados ao SW2 apresentam perda de acesso aos recursos localizados em outras VLANs.

Os dispositivos continuam conectados fisicamente.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/02-trunk-incorreto/falha.pkt


## **Alteração proposital**

No **SW2**, alterar a porta de uplink:


Fa0/24


que conecta ao SW1.

Remover o funcionamento como trunk:


interface fa0/24
 switchport mode access


## **Sintoma esperado**

As VLANs locais do SW2 continuam existindo, mas o tráfego VLAN necessário para atravessar o enlace SW2 → SW1 deixa de funcionar corretamente.

## **Diagnóstico esperado**

Investigar:


ping gateway
show vlan brief
show interfaces fa0/24 switchport
show interfaces trunk


Depois comparar o comportamento com o uplink do SW1.

## **Causa raiz**

Fa0/24 do SW2 deixou de operar como trunk.

## **Correção**


interface fa0/24
 switchport mode trunk


## **Validação**

Confirmar:


show interfaces trunk


e repetir testes de conectividade dos dispositivos afetados.

---

# **Incidente 03 — Gateway incorreto**

## **Cenário**

Um computador do setor Financeiro consegue se comunicar localmente, mas não consegue acessar recursos localizados em outras redes.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/03-gateway-incorreto/falha.pkt


## **Alteração proposital**

Configurar o gateway do **PC0** incorretamente.

Por exemplo:


IP:       192.168.10.21
Máscara:  255.255.255.0
Gateway:  192.168.10.254


em vez de:


Gateway:  192.168.10.1


## **Sintoma esperado**

O PC continua podendo apresentar comunicação dentro da própria rede local, mas não consegue encaminhar tráfego para outras redes.

## **Diagnóstico esperado**

Investigar:


ipconfig
ping 192.168.10.1
ping 192.168.40.10


Hipóteses:

* IP incorreto;
* máscara incorreta;
* gateway incorreto;
* problema de roteamento.

## **Causa raiz**

Gateway padrão incorreto no PC.

## **Correção**

Restaurar:


192.168.10.1


## **Validação**

Testar:


ping 192.168.10.1
ping 192.168.20.21
ping 192.168.40.10


---

# **Incidente 04 — DHCP incorreto**

## **Cenário**

Um computador do setor TI está conectado à rede, mas não consegue obter uma configuração IP válida automaticamente.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/04-dhcp-incorreto/falha.pkt


## **Alteração proposital**

No R1, alterar a rede do pool DHCP do TI.

O pool correto é:


ip dhcp pool TI
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 192.168.40.10


Para criar a falha, alterar a rede para uma rede incorreta, por exemplo:


network 192.168.30.0 255.255.255.0


## **Sintoma esperado**

Os dispositivos da VLAN 20 deixam de receber corretamente uma configuração pertencente à sua rede.

## **Diagnóstico esperado**

No PC afetado:


ipconfig


Depois investigar:


show ip dhcp pool
show ip dhcp binding
show running-config


Testar também a conectividade com o gateway.

## **Hipóteses**

* problema físico;
* porta na VLAN errada;
* pool DHCP incorreto;
* ausência de endereço disponível;
* gateway incorreto;
* problema no roteador.

## **Causa raiz**

Configuração incorreta do pool DHCP da VLAN 20.

## **Correção**

Restaurar:


network 192.168.20.0 255.255.255.0


## **Validação**

Renovar a configuração DHCP do dispositivo e verificar:

* endereço IP;
* máscara;
* gateway;
* DNS;
* conectividade.

---

# **Incidente 05 — DNS incorreto**

## **Cenário**

Um usuário consegue acessar servidores pelo endereço IP, mas não consegue acessar os mesmos recursos utilizando seus nomes.

Exemplo:


ping 192.168.40.11


funciona.

Enquanto:


ping intranet.empresa.local


falha.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/05-dns-incorreto/falha.pkt


## **Alteração proposital**

No DNS Server `192.168.40.10`, alterar ou remover o registro:


intranet.empresa.local


que originalmente aponta para:


192.168.40.11


Uma forma simples de reproduzir a falha é remover esse registro.

## **Sintoma esperado**

A conectividade IP continua funcionando, mas a resolução do nome deixa de funcionar.

## **Diagnóstico esperado**

Comparar:


ping 192.168.40.11
ping intranet.empresa.local


Verificar:


ipconfig


e confirmar qual DNS está sendo utilizado.

Depois verificar os registros configurados no servidor DNS.

## **Hipóteses**

* servidor Intranet indisponível;
* problema de conectividade;
* DNS incorreto no cliente;
* registro DNS ausente/incorreto;
* serviço DNS desligado.

## **Causa raiz**

Registro DNS incorreto ou ausente.

## **Correção**

Restaurar:


intranet.empresa.local → 192.168.40.11


## **Validação**

Testar:


ping intranet.empresa.local


e acessar a Intranet utilizando o hostname.

---

# **Incidente 06 — SSH incorreto**

## **Cenário**

O administrador consegue alcançar o equipamento pela rede, mas não consegue realizar administração remota via SSH.

O dispositivo responde ao ping.

## **Arquivo**

Base:


07-hardening/hardening-baseline.pkt


Criar:


08-troubleshooting/06-ssh-incorreto/falha.pkt


## **Alteração proposital**

No SW1, alterar a configuração das VTY para permitir Telnet em vez de SSH:


line vty 0 15
 transport input telnet


## **Sintoma esperado**

O SW1 continua acessível por IP, mas uma tentativa de SSH não funciona.

## **Diagnóstico esperado**

Primeiro:


ping 192.168.99.2


Depois:


show ip ssh
show running-config
show running-config | section line vty


## **Hipóteses**

* problema de conectividade;
* SSH desabilitado;
* VTY configurada incorretamente;
* autenticação local;
* chaves RSA;
* versão SSH;
* protocolo permitido nas VTY.

## **Causa raiz**

As linhas VTY não estão permitindo SSH.

## **Correção**


line vty 0 15
 login local
 transport input ssh


## **Validação**

Confirmar:


show ip ssh
show running-config | section line vty


e realizar nova conexão:


ssh -l admin 192.168.99.2


---

# **Ordem recomendada de execução**

Os incidentes devem ser trabalhados nesta ordem:


01 — VLAN
 ↓
02 — Trunk
 ↓
03 — Gateway
 ↓
04 — DHCP
 ↓
05 — DNS
 ↓
06 — SSH


A ordem foi escolhida para evoluir dos problemas de **camada 2 e conectividade básica** para problemas de **endereçamento, serviços e gerenciamento**.

---

# **Regra para criação dos arquivos de falha**

Cada arquivo deve ser criado sempre a partir de:


07-hardening/hardening-baseline.pkt


Nunca modificar o baseline original.

Processo:


hardening-baseline.pkt
        ↓
fazer uma cópia
        ↓
introduzir UMA falha
        ↓
salvar como falha.pkt
        ↓
investigar utilizando o roteiro
        ↓
corrigir
        ↓
validar


O arquivo de falha deve conter **uma causa principal**, evitando múltiplas alterações simultâneas.

---

# **Registro de cada incidente**

Para cada caso, registrar:

### **Sintoma**

O que o usuário relatou.

### **Escopo**

Quais dispositivos, VLANs ou serviços foram afetados.

### **Hipóteses**

Possíveis causas levantadas antes dos testes.

### **Testes**

Comandos e verificações realizados.

### **Evidências**

Resultados relevantes dos testes.

### **Causa raiz**

Configuração ou componente responsável pelo problema.

### **Correção**

Alteração realizada.

### **Validação**

Testes que comprovaram a resolução.

---

# **Resultado esperado da Etapa 08**

Ao finalizar os seis incidentes, o projeto deverá demonstrar não apenas conhecimento de configuração de redes, mas também capacidade de:

* interpretar sintomas;
* delimitar problemas;
* formular hipóteses;
* selecionar comandos de diagnóstico;
* interpretar resultados;
* localizar causas;
* realizar correções;
* validar soluções;
* documentar incidentes de forma técnica.
