# azure
Azure basic
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

👉 Se o `az account show` retornar informações da sua conta sem erro, significa que o login está ok.  

Quer que eu monte um **mini-checklist** de comandos para você rodar em sequência e garantir que está tudo certo com seu login?
