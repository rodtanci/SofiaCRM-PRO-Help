# 🔐 Guia de Configuração de Credenciais - SofiaCRM Pro

Este guia fornece instruções passo a passo para configurar todas as credenciais necessárias para a stack do SofiaCRM Pro no Portainer.

---

## 📋 Índice

1. [Visão Geral](#visão-geral)
2. [Pré-requisitos](#pré-requisitos)
3. [Credenciais a Configurar](#credenciais-a-configurar)
4. [Passo a Passo](#passo-a-passo)
5. [Checklist Final](#checklist-final)
6. [Troubleshooting](#troubleshooting)

---

## 🎯 Visão Geral

A stack do SofiaCRM Pro requer **8 credenciais** que devem ser configuradas antes do deploy:

| Variável | Descrição | Onde Aparece |
|----------|-----------|--------------|
| `SENHA_DO_POSTGRES` | Senha do banco de dados PostgreSQL | `crm_api` |
| `SENHA_DO_REDIS` | Senha do Redis | `crm_api` |
| `JWT_SECRET` | Token para autenticação JWT | `crm_api` |
| `INTERNAL_TOKEN` | Token de comunicação interna | `crm_api` |
| `INTERNAL_WEBHOOK_TOKEN` | Token de webhook interno | `crm_api`, `whats-service` |
| `LICENSE_TOKEN` | Token da licença Pro | `crm_api` |
| `META_CLOUD_SERVICE_TOKEN` | Token de comunicação com Meta | `crm_api`, `meta-cloud-service` |
| `URL_DO_CRM` | Domínio público do CRM | `crm_api` (labels Traefik) |

---

## ✅ Pré-requisitos

Antes de começar, certifique-se de ter:

- [ ] Acesso ao servidor via SSH
- [ ] Portainer instalado e funcionando
- [ ] Rede `swarm_network` criada
- [ ] Traefik configurado na rede
- [ ] PostgreSQL (pgvector) e Redis já em execução
- [ ] Domínio configurado apontando para o servidor
- [ ] Token de licença fornecido pela equipe SofiaCRM

---

## 🔑 Passo a Passo

### 1️⃣ Gerar SENHA_DO_POSTGRES

**Descrição:** Senha para conexão com o banco de dados PostgreSQL.

**Como gerar:**

```bash
# Via terminal SSH
openssl rand -base64 32
```

**Exemplo de resultado:**
```
kX7mN9pQ2rS5tU8vY1zA4bC7dE0fG3hI
```

**Onde substituir no arquivo:**
```yaml
DATABASE_URL: postgresql://postgres:kX7mN9pQ2rS5tU8vY1zA4bC7dE0fG3hI@pgvector:5432/crm
```

> ⚠️ **IMPORTANTE:** Esta senha deve ser a mesma configurada no seu serviço PostgreSQL/pgvector.

---

### 2️⃣ Gerar SENHA_DO_REDIS

**Descrição:** Senha para autenticação no Redis.

**Como gerar:**

```bash
# Via terminal SSH
openssl rand -base64 24
```

**Exemplo de resultado:**
```
aB3cD4eF5gH6iJ7kL8mN9oP0
```

**Onde substituir no arquivo:**
```yaml
REDIS_URL: redis://:aB3cD4eF5gH6iJ7kL8mN9oP0@redis:6379
```

> ⚠️ **IMPORTANTE:** Esta senha deve ser a mesma configurada no seu serviço Redis.

---

### 3️⃣ Gerar JWT_SECRET

**Descrição:** Chave secreta usada para assinar e verificar tokens JWT de autenticação.

**Como gerar:**

```bash
# Via terminal SSH (recomendado: 64 caracteres hexadecimais)
openssl rand -hex 32
```

**Exemplo de resultado:**
```
a1b2c3d4e5f6789012345678901234567890abcdef1234567890abcdef123456
```

**Onde substituir no arquivo:**
```yaml
JWT_SECRET: a1b2c3d4e5f6789012345678901234567890abcdef1234567890abcdef123456
```

> 🔒 **SEGURANÇA:** Nunca compartilhe este token. Se comprometido, todos os usuários precisarão fazer login novamente.

---

### 4️⃣ Gerar INTERNAL_TOKEN e INTERNAL_WEBHOOK_TOKEN

**Descrição:** Tokens para comunicação segura entre os serviços internos do CRM.

> ⚠️ **CRÍTICO:** Estes dois tokens devem ter **O MESMO VALOR** em todos os serviços!

**Como gerar:**

```bash
# Via terminal SSH
openssl rand -hex 32
```

**Exemplo de resultado:**
```
7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
```

**Onde substituir no arquivo:**

No serviço `crm_api`:
```yaml
INTERNAL_TOKEN: 7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
INTERNAL_WEBHOOK_TOKEN: 7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
```

No serviço `whats-service`:
```yaml
INTERNAL_WEBHOOK_TOKEN: 7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
```

---

### 5️⃣ Obter LICENSE_TOKEN

**Descrição:** Token de licença para ativar a versão Pro do SofiaCRM.

**Como obter:**

1. Entre em contato com a equipe SofiaCRM
2. Forneça os dados do seu CNPJ/empresa
3. Após a aprovação, você receberá o token por email

**Onde substituir no arquivo:**
```yaml
LICENSE_TOKEN: seu-token-de-licenca-recebido-por-email
```

> 📧 **Suporte:** Se você não possui um token de licença, entre em contato com suporte@sofiacrm.com.br

---

### 6️⃣ Gerar META_CLOUD_SERVICE_TOKEN

**Descrição:** Token para comunicação segura entre o CRM e o serviço Meta Cloud (WhatsApp Cloud API).

> ⚠️ **CRÍTICO:** Este token deve ser **IDÊNTICO** em `crm_api` e `meta-cloud-service`!

**Como gerar:**

**Opção 1 - Via terminal SSH:**
```bash
openssl rand -hex 32
```

**Opção 2 - Via site:**
1. Acesse: https://www.hexhero.com/tools/random-key-generator
2. Em **Options**, selecione: `Hexadecimal`
3. Em **Key Strength**, selecione: `32 Bytes`
4. Clique em **Generate**
5. Copie o resultado

**Exemplo de resultado:**
```
6d2b956c30ea7f07039fb3d9e0f7f21b8e6f3b63cbb5aaecf0e12d901d79c6ec
```

**Onde substituir no arquivo:**

No serviço `crm_api`:
```yaml
META_CLOUD_SERVICE_TOKEN: 6d2b956c30ea7f07039fb3d9e0f7f21b8e6f3b63cbb5aaecf0e12d901d79c6ec
```

No serviço `meta-cloud-service`:
```yaml
META_CLOUD_SERVICE_TOKEN: 6d2b956c30ea7f07039fb3d9e0f7f21b8e6f3b63cbb5aaecf0e12d901d79c6ec
```

---

### 7️⃣ Configurar URL_DO_CRM

**Descrição:** Domínio público onde o CRM será acessado.

**Formato:** Apenas o domínio, sem `https://` (exceto em `PUBLIC_BASE_URL`)

**Exemplo:**
```
app.suaempresa.com.br
```

**Onde substituir no arquivo:**

Na variável `PUBLIC_BASE_URL` (com https://):
```yaml
PUBLIC_BASE_URL: https://app.suaempresa.com.br
```

Nas labels do Traefik (sem https://):

No serviço `crm_api`:
```yaml
- "traefik.http.routers.crm_api.rule=Host(`app.suaempresa.com.br`)"
```

No serviço `meta-cloud-service`:
```yaml
- "traefik.http.routers.meta_webhook.rule=Host(`app.suaempresa.com.br`) && PathPrefix(`/api/webhooks/meta/whatsapp`)"
- "traefik.http.routers.meta_cloud.rule=Host(`app.suaempresa.com.br`) && PathPrefix(`/api/meta-cloud`)"
```

> 🌐 **DNS:** Certifique-se de que o domínio está apontando para o IP do seu servidor antes do deploy.

---

## ✅ Checklist Final

Antes de fazer o deploy, verifique:

- [ ] `SENHA_DO_POSTGRES` - Gerada e igual à configurada no PostgreSQL
- [ ] `SENHA_DO_REDIS` - Gerada e igual à configurada no Redis
- [ ] `JWT_SECRET` - Gerado (64 caracteres hex)
- [ ] `INTERNAL_TOKEN` - Gerado e **igual** ao `INTERNAL_WEBHOOK_TOKEN`
- [ ] `INTERNAL_WEBHOOK_TOKEN` - **Igual** em `crm_api` e `whats-service`
- [ ] `LICENSE_TOKEN` - Obtido com a equipe SofiaCRM
- [ ] `META_CLOUD_SERVICE_TOKEN` - Gerado e **igual** em `crm_api` e `meta-cloud-service`
- [ ] `URL_DO_CRM` - Configurado em todas as 4 ocorrências
- [ ] DNS do domínio apontando para o servidor
- [ ] Rede `swarm_network` existente
- [ ] PostgreSQL e Redis em execução

---

## 🔧 Troubleshooting

### Erro de conexão com PostgreSQL
- Verifique se a senha no `DATABASE_URL` é igual à configurada no PostgreSQL
- Confirme se o serviço `pgvector` está na rede `swarm_network`

### Erro de conexão com Redis
- Verifique se a senha no `REDIS_URL` é igual à configurada no Redis
- Confirme se o serviço `redis` está na rede `swarm_network`

### Erro de autenticação JWT
- Regenere o `JWT_SECRET` e reinicie o serviço
- Todos os usuários precisarão fazer login novamente

### Erro de comunicação entre serviços
- Verifique se `INTERNAL_TOKEN` e `INTERNAL_WEBHOOK_TOKEN` são idênticos
- Verifique se `META_CLOUD_SERVICE_TOKEN` é idêntico nos dois serviços

### Erro de licença inválida
- Confirme se o `LICENSE_TOKEN` foi copiado corretamente (sem espaços extras)
- Entre em contato com o suporte para validar seu token

### Erro 502/503 no Traefik
- Verifique se os serviços estão saudáveis: `docker ps`
- Verifique os logs: `docker logs crm_api`
- Confirme se a `URL_DO_CRM` está correta nas labels do Traefik

---

## 📝 Resumo de Comandos

```bash
# Gerar todas as credenciais de uma vez
echo "=== SENHA_DO_POSTGRES ==="
openssl rand -base64 32

echo "=== SENHA_DO_REDIS ==="
openssl rand -base64 24

echo "=== JWT_SECRET ==="
openssl rand -hex 32

echo "=== INTERNAL_TOKEN / INTERNAL_WEBHOOK_TOKEN ==="
openssl rand -hex 32

echo "=== META_CLOUD_SERVICE_TOKEN ==="
openssl rand -hex 32
```

---

## 📞 Suporte

Se precisar de ajuda adicional:

- **Email:** suporte@sofiacrm.com.br
- **Documentação:** https://docs.sofiacrm.com.br

---

*Última atualização: Janeiro 2026*

