# Estrutura de Frontend (Next.js)

## Organização de pastas

```txt
frontend/
  src/
    app/
      (public)/
        login/page.tsx
      (student)/
        dashboard/page.tsx
        courses/page.tsx
        courses/[id]/page.tsx
        lessons/[id]/page.tsx
        certificates/page.tsx
        messages/page.tsx
      (teacher)/
        dashboard/page.tsx
        courses/page.tsx
        courses/new/page.tsx
        courses/[id]/edit/page.tsx
        live-classes/page.tsx
        reports/page.tsx
      (admin)/
        dashboard/page.tsx
        users/page.tsx
        courses/page.tsx
        moderation/page.tsx
        reports/page.tsx
    components/
      layout/
      ui/
      course/
      lesson/
      assessment/
      notifications/
    lib/
      api-client.ts
      auth.ts
      rbac.ts
      validators.ts
    styles/
      globals.css
```

## Diretrizes UX/UI (tema escuro)

- Tema padrão escuro (`bg-zinc-950`, `text-zinc-100`).
- Contraste AA para leitura prolongada.
- Tipografia: Inter/Source Sans, line-height confortável (1.6+).
- Cards com separação clara e hierarquia visual discreta.
- Estados de progresso explícitos: **bloqueada**, **em andamento**, **concluída**.
- Player de vídeo com barra de progresso persistida automaticamente.

## Fluxos principais

1. **Aluno**
   - Login Google -> Dashboard -> Trilhas -> Aula atual -> Avaliação -> Certificado.
2. **Professor**
   - Dashboard -> Criar curso -> Criar aulas/avaliações -> Agendar aula ao vivo -> Relatórios.
3. **Administrador**
   - Gestão de usuários/professores/cursos -> Moderação -> Relatórios globais.

## Controle de acesso no frontend

- `middleware.ts` valida sessão e role para rotas sensíveis.
- Componentes de rota usam guardas (`RequireRole`) para esconder ações não autorizadas.
- Toda autorização final sempre validada no backend (frontend apenas melhora UX).
