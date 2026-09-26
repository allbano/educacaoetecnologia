# Instruções para Codex

## Layout do Repositório

- `frontend/` — Aplicação Angular 22.1 com SSR (Express 5). Este é o único pacote.
- `frontend/src/main.ts` — entrada do browser; `frontend/src/main.server.ts` — entrada do SSR.
- `frontend/src/server.ts` — servidor Express; serve estáticos de `dist/browser`, SSR para todo o resto.
- `frontend/src/app/app.routes.ts` — rotas do cliente; `frontend/src/app/app.routes.server.ts` — modos de renderização no servidor.
- Prefixo dos seletores de componentes: `app`.

## Comandos

Todos os comandos são executados a partir de `frontend/`:

| Tarefa | Comando |
|---|---|
| Servidor de desenvolvimento (porta 4200) | `npm start` |
| Build de produção | `npm run build` |
| Testes unitários (Vitest, watch) | `npm test` |
| Arquivo de teste único | `npx vitest run src/app/app.spec.ts` |
| Servidor SSR de produção (porta 4000) | `npm run serve:ssr:frontend` |

- **Não há script de lint**. Nenhum ESLint está configurado.
- O gerenciador de pacotes é **npm** (v12.1.0). Não use yarn ou pnpm.

## SSR e Prerenderização

- A aplicação usa Angular SSR com `@angular/ssr`. Todas as rotas têm como padrão `RenderMode.Prerender` em `app.routes.server.ts`.
- Ao adicionar uma nova rota, adicione-a em **ambos** `app.routes.ts` (cliente) e `app.routes.server.ts` (modo de renderização).
- O servidor SSR roda na porta **4000** (não 4200). O servidor de desenvolvimento (`ng serve`) não usa SSR.

## TypeScript

- O `tsconfig.json` habilita flags mais restritas que o padrão do Angular: `noImplicitOverride`, `noPropertyAccessFromIndexSignature`, `noImplicitReturns`, `noFallthroughCasesInSwitch`.
- `experimentalDecorators: true` está definido, mas o Angular 22 usa decoradores padrão — não use sintaxe de decorador legada.
- Arquivos de teste usam tipos `vitest/globals` (não é necessário importar `describe`, `it`, `expect`).

## Estilização

- **Tailwind CSS v4** via PostCSS (`@tailwindcss/postcss`). A importação global é `@import 'tailwindcss'` em `src/styles.css` — não as diretivas `@tailwind` da v3.
- Prettier: largura de 100 caracteres, aspas simples, parser Angular para arquivos `.html`.

## Budgets de Produção

- Bundle inicial: alerta em **500 kB**, erro em **1 MB**.
- Estilos de componente: alerta em **4 kB**, erro em **8 kB**.

## Convenções do Angular (Não Óbvias)

- **Não** defina `standalone: true` — é o padrão no Angular v20+.
- **Não** defina `changeDetection: OnPush` explicitamente — é o padrão no Angular v22+.
- **Não** use os decoradores `@HostBinding` / `@HostListener` — use o objeto `host` em `@Component` / `@Directive`.
- Use as funções de sinal `input()`, `output()`, `model()` em vez dos decoradores `@Input()`, `@Output()`.
- Use controle de fluxo nativo (`@if`, `@for`, `@switch`) — não `*ngIf`, `*ngFor`, `*ngSwitch`.
- **Não** importe `CommonModule` — importe diretivas/pipes individuais (`AsyncPipe`, `DatePipe`, etc.).
- **Não** use `ngClass` ou `ngStyle` — use bindings de `class` e `style`.
- Prefira Signal Forms (`@angular/forms/signals`) para novos formulários; como alternativa, use Reactive Forms.
- Use `inject()` em vez de injeção via construtor.
- Use `NgOptimizedImage` para imagens estáticas (não funciona com base64 inline).

## Acessibilidade

- Deve passar nas verificações do AXE e atender ao WCAG AA (gerenciamento de foco, contraste de cores, ARIA).
