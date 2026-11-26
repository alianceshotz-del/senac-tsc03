# Relatório técnico — Hardening de OpenSSH, Apache2 e MySQL Server

**Unidade curricular:** UC5 — Planejar e executar a segurança cibernética em computadores  
**Data registrada:** 26/11/2026  
**Servidor:** `svrpedro`  
**Sistema operacional:** Ubuntu Server 26.04.x LTS (64 bits)  
**Endereço IPv4:** `10.24.86.225/24`  
**Interface:** `enp0s3`  
**Estação docente:** `10.24.86.200`

> Este documento organiza o conteúdo do relatório técnico fornecido para a atividade. Credenciais e senhas não são reproduzidas nesta documentação.

## 1. Objetivo e escopo

A atividade documenta o endurecimento do acesso remoto via OpenSSH, o controle de acesso com UFW e a análise de TCP Wrappers, a gestão de contas e privilégios administrativos, além da instalação, liberação no firewall e validação dos serviços Apache2 e MySQL.

O relatório informa que os serviços requisitados estavam ativos e associados às portas TCP 22, 80 e 3306 na validação realizada.

## 2. OpenSSH e controle de acesso

### 2.1. TCP Wrappers e UFW

- Foi ajustado `/etc/hosts.allow` para a sub-rede `10.24.86.0/24`, associado ao daemon SSH.
- Foi mantida a política restritiva `ALL: ALL` em `/etc/hosts.deny`.
- A inspeção do binário SSH com `ldd /usr/sbin/sshd | grep libwrap` não identificou suporte nativo à biblioteca `libwrap` no OpenSSH utilizado.
- Por esse motivo, o relatório registra que o mecanismo de `spawn` não gerou o arquivo `/var/log/allowssh.log`; o controle efetivo e os registros de firewall ficaram a cargo do UFW e de `/var/log/ufw.log`.
- Como o ambiente foi descrito com política padrão de saída negada, o relatório também documenta a necessidade de uma regra de saída SSH da estação docente `10.24.86.200` para o servidor `10.24.86.225`, na porta TCP 22.

### 2.2. Configuração do daemon e banners

O relatório registra cópia de segurança de `/etc/ssh/sshd_config` e configuração das seguintes diretivas:

| Diretiva | Configuração documentada |
|---|---|
| `ListenAddress` | `10.24.86.225` |
| `PermitRootLogin` | `no` |
| `MaxAuthTries` | `10` |
| `Banner` | `/etc/issue.net` |

Também foram personalizados os avisos em `/etc/issue.net` para conexões remotas e em `/etc/issue` para terminais locais. O texto do banner menciona monitoramento e a LGPD (Lei nº 13.709/2018).

A validação de sintaxe foi realizada com `sudo sshd -T`, antes da reinicialização do serviço SSH.

### 2.3. Contas e privilégios

Os desafios descritos no relatório envolveram:

1. Criação da conta administrativa `admin`.
2. Inclusão de `admin` no grupo `sudo`.
3. Autorização de `admin` e `pedro` nas diretivas de acesso SSH.
4. Criação da conta nominal `pedro` e inclusão no grupo `sudo`.
5. Bloqueio da conta padrão `senac`.

O documento registra validações de grupos, da configuração efetiva do SSH e do estado bloqueado da conta `senac`. Senhas utilizadas durante a atividade foram omitidas desta cópia documental.

## 3. Apache2 Server

O relatório registra:

- Instalação do Apache HTTP Server, versão 2.4.66.
- Habilitação do serviço para iniciar com o sistema.
- Regra de entrada no UFW para TCP 80, limitada à sub-rede `10.24.86.0/24`, pela interface `enp0s3`.
- Backup da página padrão e publicação de uma página HTML personalizada para identificar o servidor.
- Teste com `curl -I http://10.24.86.225`, que retornou `HTTP/1.1 200 OK` e a identificação `Apache/2.4.66 (Ubuntu)`.

## 4. MySQL Server

O relatório registra:

- Instalação do MySQL Server, versão 8.4.13, e habilitação do serviço na inicialização.
- Regra de entrada no UFW para TCP 3306, limitada à sub-rede `10.24.86.0/24`, com registro de auditoria.
- Backup de `/etc/mysql/mysql.conf.d/mysqld.cnf`.
- Configuração de `bind-address = 0.0.0.0` para permitir escuta em todas as interfaces IPv4.
- Criação do banco de dados `db_uc5`, com conjunto de caracteres `utf8mb4`.
- Criação e concessão de privilégios ao usuário remoto `admin`@'`10.24.86.%`' (conforme descrito no relatório).
- Verificação do banco por meio de `SHOW DATABASES;`.

**Observação técnica registrada:** como o MySQL foi configurado para escutar em `0.0.0.0:3306`, a restrição de acesso à rede depende também das regras do UFW e das permissões configuradas no próprio MySQL.

## 5. Evidência consolidada de serviços

A verificação final com `sudo ss -tulpn` filtrada pelas portas 22, 80 e 3306 apresentou os seguintes sockets em estado de escuta:

| Serviço | Endereço/porta registrado |
|---|---|
| OpenSSH | `0.0.0.0:22` e `[::]:22` |
| MySQL | `0.0.0.0:3306` |
| MySQL X Protocol | `127.0.0.1:33060` |
| Apache2 | `*:80` |

## 6. Observações para auditoria

- O relatório identifica que o OpenSSH utilizado não possui suporte nativo a TCP Wrappers; portanto, os arquivos `hosts.allow` e `hosts.deny` não devem ser apresentados como controle efetivo do daemon SSH nesse ambiente.
- A configuração `bind-address = 0.0.0.0` amplia as interfaces IPv4 nas quais o MySQL escuta. A exposição efetiva depende das regras de firewall e dos privilégios do banco.
- O documento informa uma política UFW padrão de entrada e saída negadas, mas esta síntese não substitui a inspeção da listagem completa das regras ativas.
- O relatório da UC5 utiliza o hostname `svrpedro` e o endereço `10.24.86.225/24`. Esses dados são distintos dos registrados no levantamento anterior da UC8 (`srvpedro`, `10.24.69.225/24`) e devem permanecer separados na documentação.

## 7. Origem

Conteúdo organizado a partir do arquivo **“Relatório Técnico — Hardening OpenSSH, Apache2 e MySQL Server (UC5).pdf”**, com quatro páginas, fornecido para a atividade de 26/11/2026.
