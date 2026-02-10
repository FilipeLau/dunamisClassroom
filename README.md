# Dunamis Classroom — Plataforma EAD (Blueprint Arquitetural)

Este repositório contém uma proposta completa de arquitetura para uma plataforma EAD da Igreja Dunamis, cobrindo:

- Arquitetura da aplicação
- Modelo de dados e ERD
- Estrutura de banco de dados relacional
- Fluxo de autenticação e autorização (RBAC)
- Endpoints principais da API
- Estrutura sugerida do frontend
- Boas práticas de segurança e performance

## Conteúdo

- `docs/architecture.md`: visão arquitetural, regras de negócio, segurança e escalabilidade.
- `docs/erd.mmd`: diagrama ER em Mermaid.
- `database/schema.sql`: schema inicial PostgreSQL.
- `backend/openapi.yaml`: contrato inicial de API.
- `frontend/structure.md`: estrutura e fluxo UX/UI do frontend.

## Próximos passos recomendados

1. Inicializar monorepo (`apps/api`, `apps/web`, `packages/shared`).
2. Implementar autenticação Google OAuth + emissão JWT.
3. Subir infraestrutura local com PostgreSQL + Redis.
4. Implementar módulos core: cursos, aulas, progresso, avaliações.
5. Adicionar geração de certificados em PDF e notificações assíncronas.
