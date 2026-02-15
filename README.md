# 🔐 Guia de Configuração de Credenciais - SofiaCRM Pro Stack

Este guia fornece um passo a passo detalhado para preencher todas as credenciais necessárias na stack do SofiaCRM Pro para Portainer/Docker.

---

## ⚠️ AVISO IMPORTANTE - Ambiente Existente vs Nova Instalação

> **Se você já possui um ambiente configurado** (PostgreSQL e Redis já rodando), **NÃO gere novas senhas**! 
> 
> Use as credenciais que já existem no seu ambiente:
> - `SENHA_DO_POSTGRES` → Use a senha atual do seu PostgreSQL
> - `SENHA_DO_REDIS` → Use a senha atual do seu Redis
> - Outros tokens já configurados → Mantenha os valores existentes
>
> **Gerar novas credenciais é apenas para NOVAS INSTALAÇÕES do zero!**

---

## 📋 Resumo das Credenciais

| Variável | Descrição | Como Obter |
|----------|-----------|------------|
| `SENHA_DO_POSTGRES` | Senha do banco de dados PostgreSQL | 🔄 Existente ou 🆕 Token hex 32 bytes |
| `SENHA_DO_REDIS` | Senha do Redis | 🔄 Existente ou 🆕 Token hex 32 bytes |
| `SEU_JWT_TOKEN` | Token para autenticação JWT | 🔄 Existente ou 🆕 Token hex 32 bytes |
| `INTERNAL_WEBHOOK_TOKEN` | Token de webhook interno |
| `SEU_TOKEN_DA_LICENÇA` | Token da licença Pro | 🎫 Recebido na compra |
| `META_CLOUD_SERVICE_TOKEN` | Token do serviço Meta Cloud | 🔄 Existente ou 🆕 Token hex 32 bytes |
| `URL_DO_CRM` | URL pública do seu CRM | 🌐 Seu domínio |
| `SUA_REDE` | Nome da rede Docker do Traefik | 🐳 Nome da sua rede existente |

**Legenda:**
- 🔄 **Existente** = Use o valor que já está configurado no seu ambiente
- 🆕 **Novo** = Gere apenas se for uma instalação do zero

---

## 🚀 Passo a Passo

### Passo 1: Gerar Tokens de Segurança

> 🔄 **Ambiente existente?** Pule para o [Passo 2](#passo-2-organizar-os-tokens-gerados) e use suas credenciais atuais!

**Para NOVAS instalações**, você precisa gerar **5 tokens únicos** (hexadecimais de 32 bytes). 

#### Opção A: Via Terminal (SSH)

Execute o comando abaixo **5 vezes** para gerar cada token:

```bash
openssl rand -hex 32
```

**Exemplo de saída:**
```
6d2b956c30ea7f07039fb3d9e0f7f21b8e6f3b63cbb5aaecf0e12d901d79c6ec
```

#### Opção B: Via Site Online

1. Acesse: [https://www.hexhero.com/tools/random-key-generator](https://www.hexhero.com/tools/random-key-generator)
2. Em **Options**, selecione: `Hexadecimal`
3. Em **Key Strength**, selecione: `32 Bytes`
4. Clique em **Generate**
5. Copie o token gerado

---

### Passo 2: Organizar os Tokens Gerados

Anote os tokens gerados e associe cada um a uma variável:

| # | Variável | Seu Token |
|---|----------|-----------|
| 1 | `SENHA_DO_POSTGRES` | `[senha_postgres]` |
| 2 | `SENHA_DO_REDIS` | `[senha_redis]` |
| 3 | `SEU_JWT_TOKEN` | `[cole_aqui_token_1]` |
| 4 | `INTERNAL_WEBHOOK_TOKEN` | `[cole_aqui_token_2]` |
| 5 | `META_CLOUD_SERVICE_TOKEN` | `[cole_aqui_token_3]` |

---

### Passo 3: Obter o Token da Licença

O `LICENSE_TOKEN` é fornecido no momento da compra da licença Pro do SofiaCRM.

- **Onde encontrar:** E-mail de confirmação de compra ou painel do cliente
- **Formato:** Token único fornecido pela equipe SofiaCRM

> 📧 Se você não possui o token da licença, entre em contato com o suporte SofiaCRM.

---

### Passo 4: Definir a URL do CRM

A `URL_DO_CRM` é o domínio público onde seu CRM estará acessível.

**Formato esperado:** Apenas o domínio, sem `https://` e sem barra final.

| ✅ Correto | ❌ Incorreto |
|-----------|-------------|
| `app.sofiacrm.com.br` | `https://app.sofiacrm.com.br` |
| `crm.minhaempresa.com` | `crm.minhaempresa.com/` |

**Onde usar:**
- Nos labels do Traefik: `Host(\`URL_DO_CRM\`)`
- Em `PUBLIC_BASE_URL`: `https://URL_DO_CRM` (aqui **com** https://)

---

### Passo 5: Substituir na Stack

Agora substitua cada placeholder na stack pelos valores corretos:

#### 5.1 - DATABASE_URL (PostgreSQL)

**Antes:**
```yaml
DATABASE_URL: postgresql://postgres:SENHA_DO_POSTGRES@pgvector:5432/crm
```

**Depois:** (exemplo)
```yaml
DATABASE_URL: postgresql://postgres:6d2b956c30ea7f07039fb3d9e0f7f21b8e6f3b63cbb5aaecf0e12d901d79c6ec@pgvector:5432/crm
```

---

#### 5.2 - REDIS_URL

**Antes:**
```yaml
REDIS_URL: redis://:SENHA_DO_REDIS@redis:6379
```

**Depois:** (exemplo)
```yaml
REDIS_URL: redis://:a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6a7b8c9d0e1f2@redis:6379
```

---

#### 5.3 - JWT_SECRET

**Antes:**
```yaml
JWT_SECRET: SEU_JWT_TOKEN
```

**Depois:** (exemplo)
```yaml
JWT_SECRET: f1e2d3c4b5a6978089706050403020100f1e2d3c4b5a6978089706050403020
```

---

#### 5.4 - INTERNAL_TOKEN e INTERNAL_WEBHOOK_TOKEN

> ⚠️ **ATENÇÃO:** Estes dois valores devem ser **IDÊNTICOS**!

**Antes:**
```yaml
INTERNAL_TOKEN: INTERNAL_TOKEN
INTERNAL_WEBHOOK_TOKEN: INTERNAL_WEBHOOK_TOKEN
```

**Depois:** (exemplo - mesmo token nos dois)
```yaml
INTERNAL_TOKEN: 9a8b7c6d5e4f3g2h1i0j9k8l7m6n5o4p3q2r1s0t9u8v7w6x5y4z3a2b1c0d9e8
INTERNAL_WEBHOOK_TOKEN: 9a8b7c6d5e4f3g2h1i0j9k8l7m6n5o4p3q2r1s0t9u8v7w6x5y4z3a2b1c0d9e8
```

> ⚠️ **CRÍTICO:** O token **INTERNAL_WEBHOOK_TOKEN** deve ser **IDÊNTICO** em dois lugares:
> - No serviço `crm_api`
> - No serviço `whats-service`

---

#### 5.5 - LICENSE_TOKEN

**Antes:**
```yaml
LICENSE_TOKEN: SEU_TOKEN_DA_LICENÇA
```

**Depois:**
```yaml
LICENSE_TOKEN: aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
```

---

#### 5.6 - META_CLOUD_SERVICE_TOKEN

> ⚠️ **CRÍTICO:** Este token deve ser **IDÊNTICO** em dois lugares:
> - No serviço `crm_api`
> - No serviço `meta-cloud-service`

**Antes (em ambos os serviços):**
```yaml
META_CLOUD_SERVICE_TOKEN: META_CLOUD_SERVICE_TOKEN
```

**Depois (mesmo valor em ambos):**
```yaml
META_CLOUD_SERVICE_TOKEN: 1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
```

---

#### 5.7 - URL_DO_CRM

Substitua em **4 lugares** diferentes na stack:

1. **PUBLIC_BASE_URL (com https://):**
```yaml
PUBLIC_BASE_URL: https://app.sofiacrm.com.br
```

2. **Traefik label do crm_api:**
```yaml
- "traefik.http.routers.crm_api.rule=Host(`app.sofiacrm.com.br`)"
```

3. **Traefik label do meta_webhook:**
```yaml
- "traefik.http.routers.meta_webhook.rule=Host(`app.sofiacrm.com.br`) && PathPrefix(`/api/webhooks/meta/whatsapp`)"
```

4. **Traefik label do meta_cloud:**
```yaml
- "traefik.http.routers.meta_cloud.rule=Host(`app.sofiacrm.com.br`) && PathPrefix(`/api/meta-cloud`)"
```

---

### Passo 6: Configurar a Rede Docker

Substitua `SUA_REDE` pelo nome da sua rede Docker onde o Traefik está rodando.

> ⚠️ **IMPORTANTE:** A rede deve existir previamente e o Traefik deve estar conectado a ela!

**Para verificar suas redes existentes:**
```bash
docker network ls
```

**Locais onde substituir `SUA_REDE`:**

1. **Networks de cada serviço (3 lugares):**
```yaml
networks:
  - minha_rede_traefik
```

2. **Labels do Traefik (3 lugares):**
```yaml
- "traefik.swarm.network=minha_rede_traefik"
```

3. **Seção networks no final do arquivo:**
```yaml
networks:
  minha_rede_traefik:
    external: true
    name: minha_rede_traefik
```

**Exemplo completo** (se sua rede se chama `traefik_public`):
- Substitua todas as ocorrências de `SUA_REDE` por `traefik_public`

---

## ✅ Checklist Final

Antes de fazer o deploy, verifique:

- [ ] `SENHA_DO_POSTGRES` substituída (token de 64 caracteres)
- [ ] `SENHA_DO_REDIS` substituída (token de 64 caracteres)
- [ ] `JWT_SECRET` substituído (token de 64 caracteres)
- [ ] `INTERNAL_TOKEN` substituído (token de 64 caracteres)
- [ ] `INTERNAL_WEBHOOK_TOKEN` substituído (**igual ao INTERNAL_TOKEN**)
- [ ] `LICENSE_TOKEN` substituído (token da licença Pro)
- [ ] `META_CLOUD_SERVICE_TOKEN` substituído nos **2 serviços** (mesmo valor)
- [ ] `URL_DO_CRM` substituída nos **4 lugares**
- [ ] `PUBLIC_BASE_URL` com `https://` no início
- [ ] `SUA_REDE` substituída em **todos os lugares** (networks dos serviços + labels do Traefik + seção networks)
- [ ] Rede externa existe e Traefik está conectado a ela

---

## 🔒 Dicas de Segurança

1. **Nunca compartilhe** seus tokens em repositórios públicos
2. **Guarde uma cópia** segura de todos os tokens gerados
3. **Use senhas diferentes** para cada variável (exceto onde indicado que devem ser iguais)
4. **Rotacione os tokens** periodicamente para maior segurança

---

## 📞 Suporte

Em caso de dúvidas:
- Suporte: Entre em contato com a equipe SofiaCRM

---

*Documento atualizado em: Janeiro/2026*

