# FC Intelligence — preparação do ambiente

Este projeto está na branch `feature/fc-intelligence`. Não faça merge na `main` durante a fase de desenvolvimento e validação.

## Arquitetura
- Frontend: React + TypeScript + Vite
- API: Cloudflare Worker em `src/worker.ts`
- Banco compartilhado: Cloudflare D1
- IA: Gemini chamada exclusivamente pelo Worker
- Aplicativo desktop planejado: Tauri 2 (ainda não integrado)

## Estado atual
- Interface inicial e navegação implementadas.
- Worker com login, sessões, criação/listagem/exclusão de lançamentos e endpoint Gemini.
- Atualização de lançamentos via `PUT /api/entries/:id`, com isolamento por grupo e registro de auditoria.
- Migration inicial em `migrations/0001_fc_schema.sql`.
- O frontend ainda usa dados demonstrativos e precisa ser conectado à autenticação/API.
- Não há banco remoto criado, secrets configurados, deploy ou instalador Windows validado.

## Configuração Cloudflare (quando autorizado)
1. Criar um banco D1 chamado `fc-intelligence`.
2. Copiar o identificador real do banco para `wrangler.toml`, substituindo `PREENCHER_COM_ID_DO_D1`.
3. Aplicar a migration: `npx wrangler d1 migrations apply fc-intelligence --remote`.
4. Configurar os secrets `FC_SETUP_KEY` e `GEMINI_API_KEY` com valores fortes e privados.
5. Fazer deploy somente após revisar a configuração e confirmar autorização.
6. Criar os usuários Fabrício e Carol com o mesmo `groupId`, para compartilharem os lançamentos. Não colocar senhas em arquivos versionados.

## Critérios antes do primeiro teste com usuários
- [ ] Frontend integrado ao login, logout, `/api/auth/me` e `/api/entries`.
- [ ] Criar, editar e excluir lançamentos com persistência D1.
- [ ] Confirmar que Fabrício e Carol veem o mesmo conjunto de lançamentos.
- [ ] Confirmar que usuários de outro grupo não acessam os dados.
- [ ] Confirmar auditoria de criação, edição e exclusão.
- [ ] Integrar chat com Gemini e tratar erros/limites.
- [ ] Implementar importação CSV/XLSX com prévia e detecção de duplicidades.
- [ ] Validar build web e tipos TypeScript.
- [ ] Integrar Tauri 2 e gerar instalador Windows em CI.
- [ ] Testar instalação, atualização e persistência no Windows.

## Segurança
- Nunca versionar tokens, senhas, chaves de API ou dados financeiros reais.
- Usar HTTPS e secrets do Cloudflare.
- Manter o isolamento de dados por `group_id` em toda consulta e mutação.
- Não usar dados demonstrativos como se fossem recebimentos reais.
