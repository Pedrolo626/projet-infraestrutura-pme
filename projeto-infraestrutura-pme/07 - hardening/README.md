Etapa 07 — Hardening
Objetivo

Aplicar controles básicos de segurança aos dispositivos de rede, evoluindo a infraestrutura de uma rede funcional para uma rede com práticas básicas de hardening e controle de acesso administrativo.

Nesta etapa foram implementadas medidas de proteção no R1, SW1 e SW2, mantendo a conectividade e os serviços configurados nas etapas anteriores.

Conceitos trabalhados
enable secret
service password-encryption
Banner de acesso (banner motd)
Autenticação local
Proteção da porta de console
Proteção das linhas VTY
Administração exclusivamente via SSH
Desativação de portas físicas não utilizadas
Validação de configurações de segurança
Manutenção da disponibilidade dos serviços após o hardening

1. Proteção do modo privilegiado

Foi configurado enable secret nos dispositivos de rede:

enable secret senhaPrivilegiada

Essa configuração adiciona autenticação para acesso ao modo privilegiado:

Router> enable
Password:
Router#

A senha utilizada neste laboratório é fictícia e exclusiva do projeto.

2. Criptografia de senhas

Foi habilitado:

service password-encryption

Essa configuração impede que determinadas senhas configuradas no IOS sejam exibidas diretamente em texto simples no running-config.

Ela complementa o uso de secret nas credenciais administrativas.

3. Banner de acesso

Foi configurado um banner de aviso nos dispositivos:

banner motd #ACESSO RESTRITO - SOMENTE USUARIOS AUTORIZADOS#

O objetivo é representar uma política básica de controle de acesso administrativo.

4. Autenticação local

A conta administrativa local utilizada no laboratório é:

Usuário: admin
Senha: senhaForte

Essa credencial é fictícia e existe exclusivamente para este laboratório.

A senha não deve ser reutilizada em ambientes reais.

5. Proteção da porta de console

Além do acesso remoto, a autenticação local foi aplicada à porta de console:

line console 0
 login local
 exit

Dessa forma, o acesso administrativo pela console utiliza a mesma base local de usuários configurada no equipamento.

Isso evita que a console permaneça sem autenticação enquanto os acessos remotos estão protegidos.

6. Proteção das linhas VTY
R1
line vty 0 4
 login local
 transport input ssh
 exit
SW1 e SW2
line vty 0 15
 login local
 transport input ssh
 exit

Com essa configuração:

o acesso remoto utiliza autenticação local;
somente SSH é permitido;
Telnet permanece desabilitado;
o usuário administrativo configurado localmente é utilizado para autenticação.

A configuração de SSH realizada na Etapa 06 foi mantida.

7. Desativação de portas não utilizadas

Portas físicas sem equipamentos conectados foram administrativamente desativadas.

SW1

Portas utilizadas:

Fa0/1  → R1
Fa0/2  → DNS
Fa0/3  → Intranet
Fa0/4  → File/Backup
Fa0/24 → SW2

Portas não utilizadas foram desativadas:

interface range fa0/5 - 23
 shutdown

SW2

Portas utilizadas:

Fa0/2  → Printer1
Fa0/3  → Printer2
Fa0/4-6   → Financeiro
Fa0/7-9   → TI
Fa0/10-13 → Vendas
Fa0/24 → SW1

A porta Fa0/1 e as portas Fa0/14-23 foram desativadas:

interface fa0/1
 shutdown

interface range fa0/14 - 23
 shutdown

A finalidade é reduzir pontos de acesso físicos desnecessários à infraestrutura.

8. Validação

Após a aplicação das configurações, foram realizadas verificações nos dispositivos.

R1
show running-config
show ip ssh

Foram verificados:

enable secret;
service password-encryption;
banner MOTD;
usuário local;
SSH versão 2;
autenticação local nas linhas VTY;
acesso SSH;
autenticação da console.
SW1 e SW2

Foram utilizados:

show running-config
show interfaces status
show vlan brief

As verificações confirmaram:

configurações de segurança aplicadas;
VLANs preservadas;
portas utilizadas permanecendo operacionais;
portas não utilizadas administrativamente desativadas;
gerenciamento por SSH preservado.

9. Testes funcionais

Após o hardening, foram realizados testes de conectividade para garantir que as medidas de segurança não afetaram o funcionamento da infraestrutura.

Foram testados:

gateway das VLANs;
servidores;
VLAN de gerenciamento;
conectividade entre redes;
acesso aos equipamentos por SSH.

Também foi validado que o acesso remoto permanece disponível somente através de SSH.

10. Resultado

A infraestrutura passou a possuir controles básicos de segurança administrativa, incluindo:

proteção do modo privilegiado;
autenticação local;
proteção da console;
proteção das linhas VTY;
SSH como protocolo de administração remota;
Telnet desabilitado;
senhas protegidas no armazenamento do IOS;
banner de acesso;
portas físicas não utilizadas desativadas.

A conectividade e os serviços implementados nas etapas anteriores permaneceram funcionais.

Estado da infraestrutura

Após esta etapa, a PME possui:

segmentação por VLAN;
roteamento inter-VLAN;
DHCP;
DNS;
servidor HTTP/Intranet;
servidor File/Backup simulado;
VLAN de gerenciamento;
administração remota por SSH;
autenticação local;
controles básicos de hardening.

A infraestrutura está pronta para a etapa de Troubleshooting, na qual serão introduzidas falhas propositalmente para simular situações de suporte e diagnóstico em uma rede corporativa.

Baseline

Arquivo de referência desta etapa:

07-hardening/hardening-baseline.pkt

Este arquivo representa o estado funcional da infraestrutura após a aplicação e validação das medidas básicas de hardening.

Próxima etapa
08 — Troubleshooting

Serão criados incidentes propositalmente para simular problemas encontrados em uma infraestrutura de PME.

Os cenários planejados incluem:

VLAN incorreta;
trunk incorreto;
gateway incorreto;
DHCP incorreto;
DNS incorreto;
SSH incorreto.

O objetivo será aplicar uma metodologia estruturada de troubleshooting:

Sintoma → Hipótese → Testes → Causa raiz → Correção → Validação