# EcoPonto Web

Aplicação web (site público + painel administrativo) do projeto **EcoPonto Digital**, desenvolvida com Next.js (App Router), TypeScript, Tailwind CSS e shadcn/ui.

Consulte pontos de coleta de resíduos recicláveis e eletrônicos, com painel administrativo para aprovação de pontos, gestão de usuários e relatórios.

## Índice

- [Pré-requisitos](#pré-requisitos)
- [Clonando o repositório](#clonando-o-repositório)
- [Instalando as dependências](#instalando-as-dependências)
- [Configurando variáveis de ambiente](#configurando-variáveis-de-ambiente)
- [Rodando o projeto em desenvolvimento](#rodando-o-projeto-em-desenvolvimento)
- [Gerando o build de produção](#gerando-o-build-de-produção)
- [Scripts disponíveis](#scripts-disponíveis)
- [Padrão de commit (Husky + lint-staged)](#padrão-de-commit-husky--lint-staged)
- [Tecnologias utilizadas](#tecnologias-utilizadas)
- [Problemas comuns](#problemas-comuns)

## Pré-requisitos

Antes de começar, instale na sua máquina:

| Ferramenta | Versão mínima                        | Link                |
| ---------- | ------------------------------------ | ------------------- |
| Node.js    | 18.18 LTS ou superior                | https://nodejs.org  |
| npm        | 9 ou superior (já vem com o Node.js) | —                   |
| Git        | qualquer versão recente              | https://git-scm.com |

Para conferir se já tem tudo instalado, abra o terminal e rode:

```bash
node -v
npm -v
git --version
```

Se algum comando não for reconhecido, instale a ferramenta correspondente antes de continuar.

## Clonando o repositório

1. Abra o terminal na pasta onde você quer salvar o projeto (ex.: Área de Trabalho, Documentos).
2. Copie a URL do repositório no GitHub/GitLab (botão verde **Code** → **HTTPS**).
3. Rode o comando abaixo:

```bash
git clone https://github.com/feDaher/eco_ponto_web.git
```

4. Entre na pasta do projeto que acabou de ser criada:

```bash
cd eco_ponto_web
```

> Se preferir, pode dar outro nome à pasta local: `git clone <url> nome-que-eu-quiser`.

## Instalando as dependências

Este projeto usa **npm** como gerenciador de pacotes (não use `yarn` nem `pnpm` aqui, para não gerar conflito de lockfile). Dentro da pasta do projeto, rode:

```bash
npm install
```

Isso vai ler o `package.json` e o `package-lock.json` e baixar todas as dependências (Next.js, Tailwind, shadcn/ui, etc.) na pasta `node_modules/`. Pode levar alguns minutos na primeira vez.

## Configurando variáveis de ambiente

1. Copie o arquivo de exemplo:

```bash
cp .env.example .env.local
```

_(No Windows, se o comando `cp` não funcionar, copie e cole o arquivo manualmente pelo explorador de arquivos, ou use `copy .env.example .env.local` no Prompt de Comando.)_

2. Abra o `.env.local` e preencha as variáveis necessárias, por exemplo:

```env
NEXT_PUBLIC_API_URL=http://localhost:3333
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=coloque_sua_chave_aqui
```

> O `.env.local` **nunca** deve ser commitado no Git (ele já está no `.gitignore`) — cada pessoa do time usa suas próprias chaves/URLs locais.

## Rodando o projeto em desenvolvimento

Com as dependências instaladas e o `.env.local` configurado, suba o servidor de desenvolvimento:

```bash
npm run dev
```

Depois de aparecer a mensagem `Ready` no terminal, abra o navegador em:

```
http://localhost:3000
```

O servidor de desenvolvimento tem **hot reload**: qualquer alteração salva no código atualiza a página automaticamente, sem precisar reiniciar nada.

Para parar o servidor, use `Ctrl + C` no terminal.

## Gerando o build de produção

Para simular como o projeto roda em produção (útil antes de publicar ou entregar uma versão):

```bash
npm run build
npm run start
```

- `npm run build` compila e otimiza todo o projeto (gera a pasta `.next/`).
- `npm run start` sobe o servidor já com esse build otimizado, também em `http://localhost:3000`.

Se o `npm run build` terminar sem erros, o critério de aceite de "projeto builda em dev/prod" está atendido.

## Scripts disponíveis

| Comando         | O que faz                                           |
| --------------- | --------------------------------------------------- |
| `npm run dev`   | Roda o projeto em modo desenvolvimento (hot reload) |
| `npm run build` | Gera o build de produção                            |
| `npm run start` | Roda o build de produção já gerado                  |
| `npm run lint`  | Roda o ESLint para checar problemas no código       |

## Padrão de commit (Husky + lint-staged)

Este projeto usa **Husky** + **lint-staged** para rodar automaticamente o ESLint e o Prettier antes de cada commit. Isso evita que código fora do padrão (mal formatado, com erro de lint) chegue a entrar no repositório.

### Você não precisa configurar nada

Essa automação já está versionada no projeto (pasta `.husky/` e o bloco `"lint-staged"` no `package.json`). Ao rodar `npm install`, o script `"prepare": "husky"` já reativa os hooks automaticamente na sua máquina — não é preciso instalar nem configurar nada manualmente depois de clonar o repositório.

### O que acontece a cada commit

1. Você edita os arquivos normalmente.
2. `git add .` (ou `git add arquivo.tsx`) — manda as alterações para a área de staging.
3. `git commit -m "sua mensagem"` — nesse momento, o Git dispara automaticamente o hook `pre-commit`.
4. O **lint-staged** roda **só nos arquivos que estão staged** (não no projeto inteiro, para o commit ficar rápido):
   - Em `.js`/`.jsx`/`.ts`/`.tsx`: primeiro `eslint --fix` (corrige o que der automaticamente), depois `prettier --write` (formata).
   - Em `.json`/`.md`/`.css`: só `prettier --write`.
5. Se tudo passar (ou for corrigido automaticamente) → o commit é criado normalmente.
6. Se o ESLint encontrar um erro que não consegue corrigir sozinho (ex.: variável não usada) → **o commit é bloqueado** até você corrigir manualmente e tentar de novo.

### Testando manualmente (sem precisar commitar)

Para validar que está tudo funcionando sem precisar de um commit de verdade:

```bash
git add .
npx lint-staged
```

Se aparecer uma saída parecida com esta, está tudo certo:

```
✔ Preparing lint-staged...
✔ Running tasks for staged files...
✔ Applying modifications from tasks...
✔ Cleaning up temporary files...
```

### Escape de emergência (usar com moderação)

Em algum caso excepcional (ex.: um commit temporário de WIP que você sabe que está quebrado), dá para pular a verificação com:

```bash
git commit -m "wip" --no-verify
```

Evite usar isso no dia a dia — é uma porta de escape, não o fluxo padrão da equipe. (sempre que usar deverá avisar o leader do projeto).

## Tecnologias utilizadas

- [Next.js](https://nextjs.org/) (App Router)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [shadcn/ui](https://ui.shadcn.com/)
- ESLint + Prettier

## Problemas comuns

- **Erro do Turbopack no Windows** (`Turbopack is not supported on this platform` ou logs de `Code Integrity`/`Smart App Control` bloqueando `next-swc.win32-x64-msvc.node`): isso acontece quando o **Smart App Control** do Windows bloqueia o binário nativo do SWC por falta de assinatura reconhecida. Duas soluções:
  1. **Desativar o Smart App Control** (se você tiver acesso de administrador na máquina): vá em `Configurações` → `Privacidade e segurança` → `Segurança do Windows` → `Controle de aplicativos e navegador` → `Controle de aplicativo inteligente` → **Desativar**. Depois feche e abra o terminal de novo e rode `npm run dev`.
  2. **Rodar com Webpack em vez de Turbopack** (funciona sempre, sem mexer em nada do sistema):
     ```bash
     npm run dev -- --webpack
     ```
- **`npm install` falha ou trava**: apague a pasta `node_modules` e o arquivo `package-lock.json`, depois rode `npm install` de novo.
- **Porta 3000 já em uso**: rode `npm run dev -- -p 3001` para subir em outra porta.
- **Erro de variável de ambiente indefinida**: confirme que o arquivo se chama exatamente `.env.local` (e não `.env.local.txt`) e que está na raiz do projeto.
- **`git clone` pede usuário/senha**: se o repositório for privado, configure autenticação via SSH ou um Personal Access Token do GitHub.
