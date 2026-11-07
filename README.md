# azure-cli-resource-management-lab
Laboratório prático de gerenciamento imperativo de recursos no Azure via Cloud Shell (Bash) e Azure CLI (az) para as certificações AZ-900 e AZ-104: provisionamento, governança com Tags, consultas avançadas com JMESPath e operações assíncronas.


# ☁️ Azure Resource Management via Cloud Shell & Azure CLI

Este repositório contém a documentação e os scripts do laboratório prático de gerenciamento imperativo de recursos no Microsoft Azure via **Azure Cloud Shell (Bash)** e **Azure CLI (`az`)**, cobrindo os conceitos fundamentais para as certificações **AZ-900 (Azure Fundamentals)** e **AZ-104 (Azure Administrator)**.

---

## 🎯 Objetivos do Laboratório
- Navegar e utilizar o **Azure Cloud Shell** em ambiente **Bash**.
- Gerenciar assinaturas e validar contextos de conta com a Azure CLI.
- Utilizar o sistema de ajuda integrado (`az --help`) para descoberta de sintaxe e argumentos obrigatórios.
- Provisionar **Resource Groups** e **Storage Accounts** imperativamente.
- Aplicar e gerenciar **Tags (Etiquetas)** no nível de grupo e de recurso individual para governança e controle de custos.
- Filtrar e formatar a saída de dados usando **JMESPath (`--query`)** e a flag `--output`.
- Executar exclusão assíncrona de recursos com a flag `--no-wait`.

---

## 🛠️ Recursos Provisionados e Estrutura

| Recurso | Tipo | Região | Função |
| :--- | :--- | :--- | :--- |
| `rg-gp-cli-demo` | Resource Group | `mexicocentral` | Contêiner lógico de governança do laboratório |
| `stgpclidemotg26` | Storage Account (Standard_LRS) | `mexicocentral` | Conta de armazenamento (Tag: `department=development`) |
| `stgpclidemotg2602` | Storage Account (Standard_LRS) | `mexicocentral` | Conta de armazenamento (Tag: `department=operations`) |

---

## 💻 Resumo dos Comandos Utilizados


# Criar Grupo de Recursos
az group create --name rg-gp-cli-demo --location mexicocentral

# Criar Contas de Armazenamento (Locally Redundant Storage - LRS)
az storage account create \
  --name stgpclidemotg26 \
  --resource-group rg-gp-cli-demo \
  --location mexicocentral \
  --sku Standard_LRS

az storage account create \
  --name stgpclidemotg2602 \
  --resource-group rg-gp-cli-demo \
  --location mexicocentral \
  --sku Standard_LRS



# Adicionar Tags ao Grupo de Recursos
az group update \
  --name rg-gp-cli-demo \
  --tags environment=test department=it-ops

# Aplicar Tag em um recurso específico encadeando a busca do Resource ID
az resource tag \
  --tags environment=test department=development \
  --ids $(az storage account show --name stgpclidemotg26 --resource-group rg-gp-cli-demo --query id --output tsv)

# Projetar colunas personalizadas no terminal (JMESPath + Output Table)
az resource list \
  --resource-group rg-gp-cli-demo \
  --query "[].{Name:name, Department:tags.department, Environment:tags.environment}" \
  --output table

# Filtrar recursos por valor de tag específico
az resource list \
  --resource-group rg-gp-cli-demo \
  --query "[?tags.department=='development'].{Name:name, Type:type}" \
  --output table

# Contar o número de recursos no grupo
az resource list --resource-group rg-gp-cli-demo --query "length([])"


# Excluir o grupo de recursos e todos os seus itens de forma assíncrona e sem prompt
az group delete --name rg-gp-cli-demo --yes --no-wait

# Validar exclusão (retorna erro ResourceGroupNotFound quando concluído)
az group show --name rg-gp-cli-demo --output table



### 1. Autenticação e Inspeção de Conta
```bash
# Exibir a assinatura ativa em formato tabela
az account show --output table

# Listar todas as assinaturas associadas à conta
az account list --output table
