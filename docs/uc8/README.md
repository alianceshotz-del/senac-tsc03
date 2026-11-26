# UC8 — Planejar e executar auditoria de sistemas

Esta seção reúne os registros e materiais de estudo relacionados à UC8.

## Conteúdos registrados

- Preparação e atualização do Ubuntu Server 26.04.1 LTS em máquina virtual VirtualBox.
- Configuração de rede com Netplan, endereço IP estático, gateway e DNS.
- Acesso remoto por SSH.
- Inventário do sistema: distribuição, kernel, data/hora, rede, discos, memória, processador, pacotes, serviços, processos, usuários, grupos e conexões.
- Integração de Git, GitHub e VS Code para versionamento da documentação.

## Configuração de rede registrada

| Parâmetro | Valor |
|---|---|
| Interface | `enp0s3` |
| IPv4 | `10.24.69.225/24` |
| Gateway | `10.24.69.1` |
| DNS | `8.8.8.8` e `8.8.4.4` |
| Domínio de pesquisa | `senac.br` |

## Inventário técnico

O levantamento abrange sistema operacional, hostname, kernel, data/hora, interfaces, IPs, rotas, DNS, discos, partições, sistemas de arquivos, RAM, swap, CPU, pacotes instalados, atualizações, serviços, processos, usuários, grupos e conexões.

O relatório bruto está em [servidor-ubuntu-srvpedro.txt](levantamentos/servidor-ubuntu-srvpedro.txt). Os dados representam o momento da coleta e devem ser atualizados quando houver alterações.

## Pastas

- [Aulas](aulas/README.md): registros das aulas.
- [Levantamentos](levantamentos/): inventário técnico.
- [Projetos](projetos/): documentação de projetos e atividades práticas.

## Observação sobre os ambientes

Os registros da UC8 e da UC5 correspondem a atividades e momentos diferentes. Na UC8, o servidor foi identificado como `srvpedro`, com endereço `10.24.69.225/24`. No relatório da UC5, o host aparece como `svrpedro`, com endereço `10.24.86.225/24`. Não tratar esses dados como se fossem necessariamente o mesmo estado de configuração.
