

# Mostrar Status de Confirmacao de Email no Painel Admin

## O que sera feito
Adicionar uma coluna "Email" na tabela de barbearias do admin que mostra se o dono da conta confirmou o email ou nao, com um indicador visual (icone verde = confirmado, icone amarelo = pendente).

## Mudancas

### 1. Edge Function `get-company-owners`
A funcao ja busca os dados do usuario via `supabaseAdmin.auth.admin.getUserById()`. O objeto retornado inclui `email_confirmed_at`. Vamos retornar essa informacao junto com os emails.

Retorno atual: `{ ownerEmails: { "user_id": "email" } }`
Retorno novo: `{ ownerEmails: { "user_id": "email" }, emailConfirmed: { "user_id": true/false } }`

### 2. Hook `useAdminCompanies`
- Adicionar campo `email_confirmed` ao tipo `AdminCompany`
- Mapear o novo dado `emailConfirmed` da edge function para cada company

### 3. Tabela `CompaniesTable`
- Ao lado do email do dono, exibir um icone indicando se o email foi confirmado:
  - Icone verde (check) = email confirmado
  - Icone amarelo (relogio) = pendente de confirmacao
- Tooltip explicativo ao passar o mouse

---

## Detalhes Tecnicos

### Arquivo: `supabase/functions/get-company-owners/index.ts`
- No loop que busca `getUserById`, tambem guardar `email_confirmed_at` em um mapa `emailConfirmed`
- Retornar `{ ownerEmails, emailConfirmed }` onde `emailConfirmed[ownerId] = !!userData.user.email_confirmed_at`

### Arquivo: `src/hooks/useAdminCompanies.ts`
- Adicionar `email_confirmed?: boolean` na interface `AdminCompany`
- Na queryFn, ler `response.data?.emailConfirmed` e mapear para cada company

### Arquivo: `src/components/admin/CompaniesTable.tsx`
- Ao lado do email (linha 166), adicionar icone condicional baseado em `company.email_confirmed`
- Usar `CheckCircle` (verde) ou `Clock` (amarelo) com tooltip

### Redeploy
A edge function `get-company-owners` precisara ser redeployada apos a alteracao.

