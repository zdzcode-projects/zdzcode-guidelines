# Padrões e Diretrizes

Este documento descreve boas práticas e padrões recomendados para projetos mantidos pela comunidade ZDZCode.

Para convenções de commits e branches, consulte o documento [Guia de Commits](guia-de-commits.md).

## Estrutura de Repositórios

1. Utilize um `README.md` com objetivo do projeto e instruções básicas.
2. Adote uma convenção de nomes consistente para branches e commits.
3. Mantenha arquivos de configuracao e scripts de automacao em pastas dedicadas.

## Convencoes de Branches

- Use o formato `<tipo>/<descricao-curta>` ao criar branches.
  - `feature/` para novas funcionalidades.
  - `fix/` para correcoes de bugs.
  - `chore/` para tarefas de manutencao.
## Estilo de Código

- Prefira código limpo e autocomentado.
- Utilize ferramentas de formatação e linters adequadas à linguagem do projeto.
## Mensagens de Commit

1. Escreva mensagens no imperativo, em portugues.
2. Limite a primeira linha a 72 caracteres.
3. Use paragrafos adicionais para detalhes quando necessario.

## Commits

- Escreva mensagens de commit claras e objetivas.
- Utilize o tempo verbal no imperativo ("Adiciona recurso X").

## Branches

- Padronize nomes de branches com o prefixo `feature/`, `fix/` ou `docs/`.
- Evite nomes longos ou genéricos.

## Revisão de Código

1. Solicite revisão de pelo menos uma pessoa.
2. Comente trechos de código que mereçam atenção especial.

## Pull Requests

1. Descreva de forma objetiva o que está sendo alterado.
2. Inclua referência a issues quando aplicável.
3. Mantenha o escopo do PR focado em uma única tarefa.

## Revisao de Codigo

- Todo PR deve ser revisado por pelo menos uma pessoa.
- Utilize comentarios construtivos e objetivos.
- Aprove mudancas somente apos garantir que testes automatizados estejam passando.

## Issues

- Registre bugs e sugestoes por meio de issues.
- Descreva passos para reproduzir problemas ou resultados esperados.
- Utilize labels para organizar prioridades.
## Licença

Os documentos seguem a licença MIT, presente neste repositório.

## Branches

- Mantenha uma branch principal chamada `main`.
- Crie branches de funcionalidade com o prefixo `feature/`.
- Para correções de bugs utilize o prefixo `bugfix/`.

## Commits

- Escreva mensagens curtas e objetivas.
- Utilize o tempo verbal no imperativo (ex: "Adiciona nova função").
- Agrupe alterações relacionadas em um único commit sempre que possível.

## Issues

- Abra issues descrevendo claramente o problema ou proposta.
- Quando possível, associe pull requests às issues correspondentes.

## Documentação

- Todo projeto deve possuir um `README.md` detalhado.
- Utilize a pasta `docs/` para guias adicionais e referências.

## Integração Contínua

- Configure pipelines de CI para validar testes e qualidade de código.
- Automatize verificações de lint e formatação.
