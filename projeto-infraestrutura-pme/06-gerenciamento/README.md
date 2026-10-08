Etapa 06 — Gerenciamento por SSH
Objetivo

Implementar uma rede de gerenciamento separada para os equipamentos de infraestrutura e habilitar o acesso remoto seguro através do protocolo SSH.

Nesta etapa foi utilizada a VLAN 99 — GERENCIAMENTO, responsável exclusivamente pela administração dos dispositivos de rede.

Foram configurados:

R1
SW1
SW2
VLAN 99
Interfaces de gerenciamento dos switches
SSH versão 2
Usuário administrativo local
Autenticação local nas linhas VTY
Acesso remoto exclusivamente por SSH
Conceitos trabalhados
VLAN de gerenciamento
SVI (Switch Virtual Interface)
ip default-gateway
Gerenciamento remoto
SSH
SSH versão 2
Chaves RSA
Usuários locais
Linhas VTY
login local
transport input ssh
Separação entre tráfego de usuários e gerenciamento
VLAN de gerenciamento

A VLAN 99 foi criada anteriormente como:

VLAN 99 — GERENCIAMENTO
Rede: 192.168.99.0/24

Os equipamentos receberam os seguintes endereços:

Dispositivo	Interface	Endereço IP
R1	G0/0.99	192.168.99.1
SW1	VLAN 99	192.168.99.2
SW2	VLAN 99	192.168.99.3

O gateway utilizado pelos switches é:

192.168.99.1
Gerenciamento dos switches

Como SW1 e SW2 são switches Layer 2, foi criada uma SVI para a VLAN 99.

SW1
interface vlan 99
 ip address 192.168.99.2 255.255.255.0
 no shutdown

ip default-gateway 192.168.99.1
SW2
interface vlan 99
 ip address 192.168.99.3 255.255.255.0
 no shutdown

ip default-gateway 192.168.99.1

O R1 já possuía a subinterface:

G0/0.99
192.168.99.1/24
Configuração do SSH

Os três dispositivos foram configurados com:

domínio empresa.local
usuário administrativo local
chave RSA
SSH versão 2
autenticação através da base local de usuários
VTY permitindo somente SSH

Exemplo utilizado:

ip domain-name empresa.local
username admin privilege 15 secret senhaForte

crypto key generate rsa

ip ssh version 2

line vty 0 15
 login local
 transport input ssh

No R1, foram utilizadas as linhas VTY disponíveis no equipamento:

line vty 0 4
 login local
 transport input ssh
Credenciais do laboratório

As credenciais abaixo foram criadas exclusivamente para este laboratório e não são utilizadas em nenhuma outra conta ou serviço.

Usuário: admin
Senha: senhaForte

A senha é deliberadamente fictícia e possui finalidade exclusivamente didática.

Como este é um ambiente de laboratório, a credencial foi documentada neste README para permitir que outra pessoa consiga reproduzir e testar o acesso SSH ao arquivo .pkt.

Não utilizar esta credencial em sistemas, contas ou ambientes reais.

Armazenamento da senha no Cisco IOS

A configuração utiliza:

username admin privilege 15 secret senhaForte

O uso de secret faz com que a credencial não seja armazenada no running-config como texto puro.

Ao salvar e fechar o arquivo .pkt, a configuração do dispositivo permanece armazenada no projeto. Portanto, ao reabrir o laboratório, o usuário admin e sua respectiva credencial continuam disponíveis para autenticação SSH.

Validação da configuração SSH

Foi utilizado o comando:

show ip ssh

para verificar o funcionamento do serviço SSH.

A configuração foi validada com SSH versão 2.

Também foram verificadas as linhas VTY:

show running-config | section line vty

A configuração esperada contém:

login local
transport input ssh

Isso garante que a autenticação seja realizada utilizando o usuário local e que o acesso remoto seja feito através de SSH.

Validação de conectividade

A partir de um computador da rede, foram realizados testes com os três endereços de gerenciamento:

ping 192.168.99.1
ping 192.168.99.2
ping 192.168.99.3

Todos os testes foram concluídos com sucesso.

Isso confirmou a comunicação entre a rede de usuários e a VLAN de gerenciamento.

Validação do acesso remoto

A partir de um computador cliente, foram realizados acessos SSH para:

ssh -l admin 192.168.99.1
ssh -l admin 192.168.99.2
ssh -l admin 192.168.99.3

Os três acessos foram concluídos com sucesso utilizando:

Usuário: admin
Senha: senhaForte

Dessa forma, foi validado o gerenciamento remoto de:

PC
 │
 │ SSH
 ▼
VLAN 99 — GERENCIAMENTO
 │
 ├── R1  → 192.168.99.1
 ├── SW1 → 192.168.99.2
 └── SW2 → 192.168.99.3
Por que utilizar uma VLAN de gerenciamento?

Separar o gerenciamento dos equipamentos do tráfego normal dos usuários permite uma organização mais adequada da infraestrutura.

Neste projeto:

VLAN 10 → Financeiro
VLAN 20 → TI
VLAN 30 → Vendas
VLAN 40 → Servidores
VLAN 50 → Impressoras
VLAN 99 → Gerenciamento

A VLAN 99 não é destinada aos computadores dos departamentos. Seu objetivo é fornecer conectividade para administração dos equipamentos de infraestrutura.

Em uma rede corporativa real, essa separação pode ser complementada posteriormente por ACLs, políticas de acesso, autenticação centralizada e outras medidas de segurança.

Resultado

Ao final desta etapa, a infraestrutura possui:

VLAN de gerenciamento dedicada
R1 com endereço de gerenciamento
SW1 com SVI de gerenciamento
SW2 com SVI de gerenciamento
Gateway de gerenciamento configurado
SSH versão 2
Chaves RSA
Usuário administrativo local
Autenticação local nas VTY
Acesso remoto aos três equipamentos
Telnet desabilitado como método de entrada nas VTY
Estado da rede

Até o momento foram implementados:

Topologia física
VLANs e segmentação
Trunks
Roteamento inter-VLAN
DHCP
DNS
HTTP/Intranet
Servidores com IP estático
VLAN de gerenciamento
Gerenciamento remoto por SSH

Ainda não foram implementados:

Hardening completo dos dispositivos
Simulação de conexão com ISP/Internet
Incidentes de troubleshooting específicos desta infraestrutura
Baseline

Arquivo salvo como:

06-gerenciamento/ssh-gerenciamento.pkt

Este arquivo representa o estado funcional da infraestrutura após a implementação e validação do gerenciamento remoto por SSH.

Próxima etapa
Etapa 07 — Hardening

A próxima etapa será dedicada ao hardening básico dos equipamentos de rede.

Serão trabalhados mecanismos como:

proteção do modo privilegiado
criptografia das senhas no running-config
banners de acesso
segurança das linhas VTY
desativação de serviços desnecessários
boas práticas adicionais de segurança do Cisco IOS