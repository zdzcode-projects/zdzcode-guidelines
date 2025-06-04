# Padrões e Diretrizes

Este documento descreve boas práticas e padrões recomendados para projetos mantidos pela comunidade ZDZCode.

## Estrutura de Repositórios

1. Utilize um `README.md` com objetivo do projeto e instruções básicas.
2. Adote uma convenção de nomes consistente para branches e commits.

## Estilo de Código

- Prefira código limpo e autocomentado.
- Utilize ferramentas de formatação e linters adequadas à linguagem do projeto.

## Pull Requests

1. Descreva de forma objetiva o que está sendo alterado.
2. Inclua referência a issues quando aplicável.
3. Mantenha o escopo do PR focado em uma única tarefa.

## Gerenciamento de Dependências

- Utilize arquivos de bloqueio (lockfiles) sempre que a linguagem oferecer
  suporte. Isso garante reprodutibilidade de builds.
- Mantenha as dependências atualizadas e remova pacotes não utilizados.

## Testes Automatizados

- Crie testes unitários e de integração sempre que possível.
- Automatize a execução dos testes em pipelines de CI.

## Integração Contínua (CI/CD)

- Configure pipelines para lint, testes e builds.
- Aplique deploy automático apenas em branches estáveis.

## Versionamento Semântico

- Siga as recomendações do [SemVer](https://semver.org/lang/pt-BR/).
- Registre as mudanças relevantes em um arquivo `CHANGELOG.md`.

## Documentação

- Documente a API e funcionalidades principais do projeto.
- Mantenha exemplos de uso atualizados.

## Licença

Os documentos seguem a licença MIT, presente neste repositório.
