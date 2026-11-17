# Levantamento inicial do Firewall UFW — 17/11/2026

## 1. Objetivo

Registrar as verificações realizadas no Ubuntu Server durante o levantamento inicial do firewall UFW. Este documento registra somente os comandos efetivamente executados e os resultados apresentados nesta atividade. Não representa a atividade separada de Blindagem Full mencionada em outro arquivo.

## 2. Identificação do ambiente

| Item | Informação observada |
|---|---|
| Distribuição | Ubuntu Server (versão não consultada nesta sessão) |
| Hostname no prompt | `svrpedro` |
| Usuário da sessão | `root` |
| Firewall | UFW |
| Versão do UFW | `0.36.2` |
| Backend iptables selecionado | `iptables-nft` |
| Interface principal | `enp0s3` |
| IPv4 do servidor | `10.24.86.225/24` |
| IPv6 link-local | `fe80::a00:27ff:fe7a:ea42/64` |
| Rede local | `10.24.86.0/24` |
| Gateway padrão | `10.24.86.1` |

**Observação sobre o hostname:** o inventário técnico anterior contém `srvpedro`, enquanto o prompt desta sessão apresentou `svrpedro`. A divergência deve ser conferida no servidor; este relatório preserva o valor observado no prompt desta sessão.

## 3. Comandos executados e resultados

### 3.1. Seleção/consulta do backend iptables

**Comando:**

```bash
sudo update-alternatives --config iptables
```

**Resultado apresentado:**

- `/usr/sbin/iptables-nft`: prioridade 20, modo automático, opção selecionada.
- `/usr/sbin/iptables-legacy`: prioridade 10, modo manual.
- `/usr/sbin/iptables-nft`: prioridade 20, modo manual.

Nenhuma opção foi escolhida manualmente; a seleção automática existente foi mantida.

### 3.2. Versão do UFW

**Comando:**

```bash
sudo ufw version
```

**Resultado:**

```text
ufw 0.36.2
Copyright 2008-2023 Canonical Ltd.
```

### 3.3. Estado e regras do UFW

**Comando:**

```bash
sudo ufw status
```

**Resultado:**

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       10.24.86.26
```

O UFW já estava ativo quando o levantamento começou. A regra observada permite conexões de entrada TCP na porta 22 com origem em `10.24.86.26`. Não foi confirmado nesta sessão se esse endereço pertence ao computador cliente SSH.

### 3.4. Estado do serviço systemd

**Comandos executados:**

```bash
sudo systemctl status ufw
sudo systemctl status ufw --no-pager
```

**Resultados relevantes:**

- Unidade: `ufw.service`.
- Carregamento: `loaded`.
- Inicialização automática: `enabled`.
- Estado: `active (exited)`.
- Processo de inicialização: `status=0/SUCCESS`.
- PID informado no resultado: `763`.
- Memória máxima informada: `2.3M`.
- Inicialização mostrada: 17/11/2026 às 17:40:25 UTC.

O estado `active (exited)` indica que a tarefa de inicialização do serviço terminou com sucesso; não significa, por si só, que o firewall esteja desligado. O estado do firewall também foi confirmado por `ufw status` como `active`.

### 3.5. Registros do serviço

**Comando:**

```bash
sudo journalctl -u ufw -n 20 --no-pager
```

**Registros apresentados:** inicializações concluídas em 26/10/2026, em diferentes inicializações de 28/10/2026, em 12/11/2026 e em 17/11/2026. As mensagens exibidas incluíram `Starting ufw.service` e `Finished ufw.service`. Não foram mostradas mensagens explícitas de falha no trecho retornado.

### 3.6. Políticas e logging

**Comando:**

```bash
sudo ufw status verbose
```

**Resultado:**

```text
Status: active
Logging: on (medium)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    10.24.86.26
```

Interpretação:

| Item | Valor observado | Interpretação |
|---|---|---|
| Status | `active` | UFW ativo |
| Logging | `on (medium)` | Registro habilitado em nível médio |
| Incoming | `deny` | Bloqueio de entrada por padrão |
| Outgoing | `allow` | Saída permitida por padrão |
| Routed | `disabled` | Encaminhamento pelo UFW desabilitado |
| New profiles | `skip` | Novos perfis de aplicação são ignorados |

### 3.7. Regras numeradas

**Comando:**

```bash
sudo ufw status numbered
```

**Resultado:**

```text
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    10.24.86.26
```

Foi apresentada uma regra numerada: regra `[1]`, permitindo `22/tcp` a partir de `10.24.86.26`.

### 3.8. Endereços das interfaces

**Comando:**

```bash
ip -br address
```

**Resultado:**

```text
lo       UNKNOWN   127.0.0.1/8 ::1/128
enp0s3   UP        10.24.86.225/24 fe80::a00:27ff:fe7a:ea42/64
```

A interface de rede principal observada é `enp0s3`, em estado `UP`.

### 3.9. Rotas

**Comando:**

```bash
ip route
```

**Resultado:**

```text
default via 10.24.86.1 dev enp0s3 proto static
10.24.86.0/24 dev enp0s3 proto kernel scope link src 10.24.86.225
```

O gateway padrão é `10.24.86.1`; a rede diretamente conectada é `10.24.86.0/24`, pela interface `enp0s3`.

## 4. Lista consolidada dos comandos executados

```bash
sudo update-alternatives --config iptables
sudo ufw version
sudo ufw status
sudo systemctl status ufw
sudo systemctl status ufw --no-pager
sudo journalctl -u ufw -n 20 --no-pager
sudo ufw status verbose
sudo ufw status numbered
ip -br address
ip route
```

## 5. Situação ao final deste levantamento

- UFW ativo.
- Serviço configurado como habilitado para inicialização automática.
- Política padrão de entrada: `deny`.
- Política padrão de saída: `allow`.
- Encaminhamento: `disabled`.
- Logging: habilitado em nível `medium`.
- Uma regra de entrada observada: `22/tcp ALLOW IN` a partir de `10.24.86.26`.
- Interface principal: `enp0s3`.
- Endereço IPv4 do servidor: `10.24.86.225/24`.
- Gateway: `10.24.86.1`.

## 6. Alterações não realizadas nesta sessão

Nesta sessão foram executados comandos de consulta. Não foram alteradas as políticas padrão, adicionadas ou removidas regras, nem executados comandos de habilitação, desabilitação, parada ou reinicialização do UFW.

A atividade separada de Blindagem Full, mencionada em outro documento, não foi incorporada a este registro e não deve ser confundida com este levantamento.

## 7. Pendências e cuidados para a próxima etapa

1. Confirmar se `10.24.86.26` é o IP do computador que inicia a conexão SSH.
2. Confirmar a divergência de hostname entre `srvpedro` no inventário anterior e `svrpedro` no prompt desta sessão.
3. Antes de alterar a política de saída para `deny`, identificar e planejar as permissões necessárias para DNS, atualizações e demais comunicações do servidor.
4. Antes de qualquer alteração que possa afetar SSH, garantir acesso alternativo pelo console da máquina virtual ou uma sessão de teste independente.
5. Adaptar exemplos do material didático à interface `enp0s3` e à rede atual `10.24.86.0/24`; não copiar interfaces ou endereços de outros ambientes sem validação.

## 8. Conclusão

O levantamento inicial confirmou o funcionamento do UFW 0.36.2 no Ubuntu Server, sua inicialização automática e as políticas e regras atualmente observadas. A configuração foi apenas consultada nesta sessão, sem alterações. As próximas etapas devem ser planejadas com base na rede efetiva do servidor e com cuidado para preservar o acesso administrativo.
