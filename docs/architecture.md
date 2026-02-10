# Plataforma EAD Dunamis — Arquitetura de Referência

## 1) Visão Geral

A solução é baseada em **arquitetura modular orientada a domínios** com separação clara entre:

- **Frontend Web** (portal de aluno/professor/admin)
- **Backend API** (REST + eventos assíncronos)
- **Banco relacional** (PostgreSQL)
- **Cache e filas** (Redis + BullMQ)
- **Serviços externos** (Google OAuth, YouTube Embed, Gmail/SES, Google Meet links)

### Objetivos não funcionais

- Escalabilidade horizontal no backend
- Segurança por padrão (RBAC + políticas por recurso)
- Observabilidade (logs estruturados, métricas e tracing)
- Boa performance em leitura (cache, paginação, índices)
- Evolução segura (migrações versionadas)

## 2) Stack Tecnológica Sugerida

### Backend

- **Node.js + NestJS (TypeScript)**
- **Prisma ORM** (ou TypeORM se preferirem padrões mais OOP)
- **PostgreSQL 15+**
- **Redis + BullMQ** para notificações e jobs
- **JWT + Google OAuth 2.0**
- **OpenAPI/Swagger** para documentação

### Frontend

- **Next.js 14+ (React + TypeScript)**
- **TailwindCSS + shadcn/ui** para UI moderna
- **Tema escuro default** com tokens de design
- **React Query** para cache de dados
- **Zustand** para estado local leve (quando necessário)

### Infra/DevOps

- Docker/Docker Compose (dev)
- CI/CD (GitHub Actions)
- Deploy: Kubernetes/ECS (produção)
- Armazenamento de arquivos: S3/Cloud Storage
- CDN para assets e PDFs

## 3) Arquitetura em Camadas

1. **Presentation Layer**: Controllers (REST) + DTOs
2. **Application Layer**: Use Cases (regras de negócio)
3. **Domain Layer**: Entidades, policies e serviços de domínio
4. **Infrastructure Layer**: Repositórios, integrações externas, filas e storage

### Módulos de domínio

- Identity & Access (usuários, papéis, permissões)
- Courses & Learning Paths (cursos, trilhas, pré-requisitos)
- Lessons & Progress (aulas, travas sequenciais, progresso)
- Assessments (provas, tentativas, notas)
- Certificates (geração de PDF)
- Live Classes (agenda, inscrições, presença)
- Messaging & Comments
- Notifications (in-app + email)
- Reporting & Analytics

## 4) Regras de Negócio-Chave

1. **Acesso sequencial de aulas**:
   - Aula N só libera se Aula N-1 foi concluída.
2. **Pré-requisito entre cursos**:
   - Matrícula/acesso bloqueado até concluir cursos exigidos.
3. **Avaliação como trava**:
   - Nota mínima obrigatória configurável por avaliação.
   - Reprovação bloqueia avanço até nova tentativa.
4. **Certificação automática**:
   - Emitida após conclusão de todas as aulas + aprovação nas avaliações obrigatórias.
5. **RBAC + escopo por recurso**:
   - Professor gerencia apenas cursos sob sua responsabilidade.
   - Admin com acesso global.

## 5) Fluxo de Autenticação e Permissões

1. Usuário clica em “Entrar com Google”
2. Frontend chama backend `/auth/google` (redirect)
3. Google retorna `code` para callback
4. Backend valida `code`, busca perfil Google, cria/atualiza usuário
5. Backend emite:
   - **Access Token JWT** (curta duração)
   - **Refresh Token** (persistido com rotação)
6. Frontend usa token nas chamadas API
7. Guardas de autorização verificam:
   - Papel (`student`, `teacher`, `admin`)
   - Permissão por ação
   - Propriedade do recurso (ex: curso do professor)

## 6) Segurança (Boas Práticas)

- Senhas não aplicáveis para OAuth-only; tokens protegidos com rotação e revogação
- Cookies `httpOnly`, `secure`, `sameSite=lax` (ou strict conforme fluxo)
- CSRF protection se usar sessão/cookies em mutações
- Rate limiting em login, mensagens, comentários
- Validação de payload (class-validator/zod)
- Sanitização de conteúdo textual para evitar XSS
- RBAC com policy-based access control (PBAC complementar)
- Logs de auditoria para ações administrativas
- Criptografia at-rest (disco/DB) e in-transit (TLS)

## 7) Performance e Escalabilidade

- Cache Redis para catálogo de cursos e trilhas
- Paginação cursor-based para listagens grandes
- Índices para consultas críticas (progresso, matrículas, relatórios)
- Filas assíncronas para:
  - Envio de emails
  - Geração de certificados PDF
  - Processamento de notificações
- Pré-cálculo de métricas de relatórios via jobs periódicos

## 8) Observabilidade

- Logging estruturado (JSON)
- Correlation ID por request
- Métricas (latência p95, throughput, erro por endpoint)
- Tracing distribuído (OpenTelemetry)
- Alertas para falhas de fila e aumento de erro 5xx

## 9) Estratégia de Entrega (Roadmap)

### Fase 1 (MVP)
- OAuth Google, RBAC base
- Cursos, aulas sequenciais, progresso
- Avaliações objetivas
- Certificado PDF
- Notificação interna + email

### Fase 2
- Aulas ao vivo com inscrições e presença
- Mensagens privadas e comentários moderáveis
- Relatórios do professor

### Fase 3
- Relatórios globais avançados
- Otimizações de performance e analytics avançado
- Recursos mobile-first/PWA
