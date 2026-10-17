# azure
Azure basic


## Install Windows Azure CLI

• Via WinGet (PowerShell ou Prompt de Comando):bash
```
winget install Microsoft.AzureCLI
```

Para verificar se o login no **Azure CLI** está funcionando corretamente, você pode usar alguns comandos bem básicos:

### 🔑 Comandos essenciais
- **Fazer login (se ainda não fez):**
  ```bash
  az login
  ```
  → Abre o navegador para autenticação.  

- **Checar a conta logada:**
  ```bash
  az account show
  ```
  → Mostra detalhes da conta atual (ID, nome, tenant).  

- **Listar todas as contas disponíveis:**
  ```bash
  az account list
  ```
  → Exibe todas as assinaturas às quais você tem acesso.  

- **Ver assinatura ativa:**
  ```bash
  az account show --query "{subscriptionName:name, subscriptionId:id}"
  ```
  → Retorna apenas o nome e ID da assinatura em uso.  

- **Trocar de assinatura (se tiver mais de uma):**
  ```bash
  az account set --subscription <ID-ou-Nome>
  ```

### 🔑 Passos básicos
1. **Login no Azure CLI**  
   ```bash
   az login
   ```

2. **Verificar conta ativa**  
   ```bash
   az account show
   ```

3. **Obter token de acesso para Microsoft Graph**  
   ```bash
   az account get-access-token --resource-type ms-graph
   ```

   Esse comando retorna um token JWT que você pode usar para chamar a API do OneDrive.

4. **Usar o token para consultar arquivos no OneDrive**  
   Exemplo com `curl`:
   ```bash
   curl -H "Authorization: Bearer <TOKEN>" https://graph.microsoft.com/v1.0/me/drive/root/children
   ```
   → Isso lista os arquivos e pastas na raiz do seu OneDrive.

### 🔑 Passo a passo no PowerShell

1. **Instalar o módulo Microsoft Graph (se ainda não tiver):**
   ```powershell
   Install-Module Microsoft.Graph -Scope CurrentUser
   ```

2. **Importar o módulo:**
   ```powershell
   Import-Module Microsoft.Graph
   ```

3. **Conectar ao Microsoft Graph (vai abrir login):**
   ```powershell
   Connect-MgGraph -Scopes "Files.Read"
   ```

   > O escopo `"Files.Read"` dá permissão de leitura aos seus arquivos do OneDrive.

4. **Listar arquivos da raiz do OneDrive:**
   ```powershell
   Get-MgDriveRootChild
   ```

   → Isso retorna os arquivos e pastas na raiz do seu OneDrive.

5. **Exemplo: listar apenas nomes dos arquivos:**
   ```powershell
   Get-MgDriveRootChild | Select-Object Name
   ```

---

📌 **Observação:**  
Se você quiser acessar pastas específicas, pode usar:
```powershell
Get-MgDriveItemChild -DriveId (Get-MgDrive).Id -ItemId <ID_da_pasta>
```

---

Aqui está um **script em PowerShell** que solicita o token de acesso via Azure CLI, e depois usa esse token para listar os arquivos e pastas na raiz do seu OneDrive através da **Microsoft Graph API**.

```powershell
# Solicita login no Azure CLI (se ainda não estiver logado)
az login

# Obtém o token de acesso para o Microsoft Graph
$token = az account get-access-token --resource-type ms-graph --query accessToken -o tsv

# Usa o token para consultar os arquivos e pastas na raiz do OneDrive
$headers = @{
    Authorization = "Bearer $token"
}

# Endpoint da API do OneDrive (raiz do drive do usuário)
$uri = "https://graph.microsoft.com/v1.0/me/drive/root/children"

# Faz a requisição e mostra os resultados
$response = Invoke-RestMethod -Uri $uri -Headers $headers -Method Get

# Exibe os nomes dos arquivos e pastas
$response.value | Select-Object name, folder, file
```

### 🔎 O que esse script faz:
1. **`az login`** → abre o navegador para autenticação, se necessário.  
2. **`az account get-access-token`** → pega o token JWT válido para o Microsoft Graph.  
3. **`Invoke-RestMethod`** → chama a API do OneDrive e retorna os itens da raiz.  
4. **`Select-Object`** → mostra apenas nome e tipo (arquivo ou pasta).  

---

👉 Se quiser, dá para adaptar para **exportar os resultados para CSV** com:
```powershell
$response.value | Select-Object name, folder, file | Export-Csv -Path "OneDriveFiles.csv" -NoTypeInformation
```

Assim você gera um inventário dos arquivos/pastas direto no seu computador. 

Aqui está um **script PowerShell completo** que já conecta, solicita o token via Azure CLI, consulta os arquivos/pastas da raiz do OneDrive e exporta tudo para um CSV:

```powershell
# Faz login no Azure CLI (se necessário)
az login

# Obtém o token de acesso para o Microsoft Graph
$token = az account get-access-token --resource-type ms-graph --query accessToken -o tsv

# Define cabeçalho de autorização
$headers = @{
    Authorization = "Bearer $token"
}

# Endpoint da API do OneDrive (raiz do drive do usuário)
$uri = "https://graph.microsoft.com/v1.0/me/drive/root/children"

# Faz a requisição
$response = Invoke-RestMethod -Uri $uri -Headers $headers -Method Get

# Exibe os resultados no console
$response.value | Select-Object name, folder, file, size

# Exporta para CSV
$response.value | Select-Object name, folder, file, size | Export-Csv -Path "OneDriveFiles.csv" -NoTypeInformation -Encoding UTF8

Write-Host "Exportação concluída! Arquivo salvo em OneDriveFiles.csv"
```

### 🔎 O que você terá:
- **Login automático** via `az login`  
- **Token JWT** obtido com `az account get-access-token`  
- **Consulta ao Microsoft Graph** para listar arquivos/pastas  
- **Exportação para CSV** com nome, tipo (arquivo/pasta) e tamanho  

👉 Depois de rodar, você encontrará o arquivo `OneDriveFiles.csv` na pasta onde executou o script.  



