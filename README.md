# Guia: Configuração de uma instância de Banco de Dados no Azure

Este repositório contém um guia prático para configurar uma instância gerenciada de SQL no Microsoft Azure. O conteúdo foi desenvolvido como parte de um desafio de laboratório e serve como material de apoio para estudos e implementações futuras.

## Índice
- [Introdução](#introdução)
- [Pré-requisitos](#pré-requisitos)
- [Passo a Passo](#passo-a-passo)
- [Dicas e Boas Práticas](#dicas-e-boas-práticas)
- [Resolução de Problemas Comuns](#resolução-de-problemas-comuns)
- [Recursos Adicionais](#recursos-adicionais)

## Introdução

O Azure SQL Managed Instance é um serviço de banco de dados gerenciado que oferece compatibilidade quase completa com o SQL Server local, enquanto fornece todos os benefícios de uma plataforma como serviço (PaaS). Este guia mostra como configurar uma instância rapidamente usando o Portal do Azure.

## Pré-requisitos

- Uma conta ativa do Microsoft Azure
- Permissões de contribuinte ou proprietário na assinatura
- Conhecimentos básicos em redes e SQL Server

## Passo a Passo

### 1. Criar uma Rede Virtual

A instância gerenciada de SQL deve ser implantada dentro de uma rede virtual:

1. No Portal do Azure, clique em **Criar um recurso**
2. Pesquise por **Rede virtual** e selecione
3. Configure os seguintes parâmetros básicos:
   - **Assinatura**: Sua assinatura do Azure
   - **Grupo de recursos**: Crie novo ou use existente
   - **Nome**: Um nome descritivo (ex: `sql-mi-vnet`)
   - **Região**: Escolha uma região disponível para SQL MI
4. Na seção de **Espaço de endereço IP**, defina:
   - Espaço de endereço: `10.0.0.0/16` (ou outro CIDR adequado)
5. Crie uma sub-rede com as seguintes configurações:
   - **Nome**: `ManagedInstance`
   - **Intervalo de endereços**: `10.0.0.0/24`
6. Clique em **Revisar + criar** e depois em **Criar**

### 2. Configurar Grupo de Segurança de Rede

1. No Portal do Azure, procure por **Grupo de segurança de rede**
2. Clique em **Criar**
3. Configure os detalhes básicos:
   - **Grupo de recursos**: O mesmo da rede virtual
   - **Nome**: `sql-mi-nsg`
   - **Região**: A mesma da rede virtual
4. Clique em **Revisar + criar** e depois em **Criar**
5. Após a criação, vá para o recurso e adicione regras de entrada/saída conforme necessário
6. Associe este NSG à sub-rede da instância gerenciada

### 3. Criar a Instância Gerenciada de SQL

1. No Portal do Azure, clique em **Criar um recurso**
2. Pesquise **Instância gerenciada de SQL do Azure** e selecione
3. Configure os detalhes básicos:
   - **Assinatura**: Sua assinatura do Azure
   - **Grupo de recursos**: O mesmo usado anteriormente
   - **Nome da instância gerenciada**: Nome único (ex: `sql-mi-demo`)
   - **Região**: A mesma da rede virtual
   - **Nível de computação**: Escolha entre Gen5 ou Gen8
   - **vCores**: Selecione com base na sua carga de trabalho (4, 8, 16, etc.)
   - **Armazenamento**: Configure conforme necessidade (32GB até 16TB)
4. Na seção **Rede**, selecione:
   - **Rede virtual**: A rede criada anteriormente
   - **Sub-rede**: `ManagedInstance`
5. Na seção **Segurança**:
   - **Login de administrador**: Crie um nome de usuário
   - **Senha**: Crie uma senha forte
6. Revise todas as configurações e clique em **Criar**

> **Observação**: A implantação pode levar de 4 a 6 horas para ser concluída.

### 4. Conectar à Instância Gerenciada

Após a implantação:

1. Localize o **nome do host** na página de visão geral da instância gerenciada
2. Configure uma máquina virtual do Azure na mesma rede virtual ou configure o Azure Bastion
3. Use o SQL Server Management Studio (SSMS) ou Azure Data Studio para se conectar usando:
   - **Servidor**: Nome do host completo
   - **Autenticação**: SQL Server
   - **Login**: Nome de usuário do administrador
   - **Senha**: A senha configurada

## Dicas e Boas Práticas

- **Tamanho da Sub-rede**: Reserve uma sub-rede grande o suficiente para crescimento futuro (pelo menos /27 ou maior)
- **Backup e Recuperação**: Configure políticas de retenção de backup adequadas às suas necessidades
- **Monitoramento**: Utilize o Azure Monitor e SQL Insights para acompanhar o desempenho
- **Alta disponibilidade**: As instâncias gerenciadas já incluem alta disponibilidade com SLA de 99,99%
- **Segurança**: Habilite o Azure Defender para SQL para proteção avançada

## Resolução de Problemas Comuns

| Problema | Solução |
|----------|---------|
| Falha na criação da instância | Verifique configurações de rede e permissões |
| Problemas de conectividade | Verifique NSGs, rotas e configurações de firewall |
| Desempenho lento | Analise consultas, índices e considere aumentar vCores |
| Erros de quota | Solicite aumento de cota ao suporte Azure |

## Recursos Adicionais

- [Documentação oficial do Azure SQL Managed Instance](https://learn.microsoft.com/pt-br/azure/azure-sql/managed-instance/)
- [Arquitetura de referência](https://learn.microsoft.com/pt-br/azure/architecture/reference-architectures/sql-server/sql-mi-landing-zone)
- [Melhores práticas de desempenho](https://learn.microsoft.com/pt-br/azure/azure-sql/managed-instance/performance-guidance)
- [Considerações de segurança](https://learn.microsoft.com/pt-br/azure/azure-sql/database/security-overview)
