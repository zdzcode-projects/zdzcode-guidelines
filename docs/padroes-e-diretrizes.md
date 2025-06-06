# Padrões e Diretrizes

Este documento descreve boas práticas e padrões recomendados para projetos mantidos pela comunidade ZDZCode.

Para convenções de commits e branches, consulte o documento [Guia de Commits](guia-de-commits.md).

## Estrutura de Repositórios

1. Utilize um `README.md` com objetivo do projeto e instruções básicas.
2. Adote uma convenção de nomes consistente para branches e commits.
3. Mantenha arquivos de configuracao e scripts de automacao em pastas dedicadas.

## Branches

- Mantenha uma branch principal chamada `main`.
- Utilize o formato `<tipo>/<descricao-curta>` ao nomear branches. Exemplos de tipos:
    - `feature/`: para novas funcionalidades.
    - `fix/` ou `bugfix/`: para correções de bugs.
    - `docs/`: para alterações na documentação.
    - `chore/`: para tarefas de manutenção e outras alterações que não se encaixam nas categorias anteriores.
- Evite nomes longos ou genéricos.
## Estilo de Código

- Prefira código limpo e autocomentado.
- Utilize ferramentas de formatação e linters adequadas à linguagem do projeto.
## Commits

- Escreva mensagens de commit claras, objetivas e no imperativo (ex: "Adiciona funcionalidade X" em vez de "Adicionando funcionalidade X" ou "Funcionalidade X adicionada").
- As mensagens devem ser em português.
- Limite a primeira linha da mensagem a 72 caracteres.
- Utilize parágrafos adicionais para fornecer detalhes quando necessário.
- Agrupe alterações relacionadas em um único commit sempre que possível.

## Pull Requests

- Descreva de forma objetiva o que está sendo alterado no Pull Request.
- Inclua referência a issues quando aplicável.
- Mantenha o escopo do PR focado em uma única tarefa.

## Revisão de Código

- Todo Pull Request (PR) deve ser revisado por pelo menos uma pessoa.
- Solicite revisão e, ao criar o PR, comente trechos de código que mereçam atenção especial.
- Durante a revisão, utilize comentários construtivos e objetivos.
- Aprove mudanças somente após garantir que os testes automatizados estejam passando e que as discussões tenham sido resolvidas.

## Issues

- Registre bugs, sugestões e propostas por meio de issues.
- Descreva claramente o problema, incluindo passos para reproduzir (no caso de bugs) ou os resultados esperados (para sugestões/propostas).
- Utilize labels para organizar e priorizar as issues.
- Quando possível, associe pull requests às issues correspondentes.
## Licença

Os documentos seguem a licença MIT, presente neste repositório.

## Documentação

- Todo projeto deve possuir um `README.md` detalhado.
- Utilize a pasta `docs/` para guias adicionais e referências.

## Integração Contínua

- Configure pipelines de CI para validar testes e qualidade de código.
- Automatize verificações de lint e formatação.
