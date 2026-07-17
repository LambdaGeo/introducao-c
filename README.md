# C para Programadores Python e VisuAlg

Site MkDocs, adaptado a partir de ["C for Python Programmers"](https://www.cs.toronto.edu/~patitsas/cs190/c_for_python.html), de Carl Burch (Hendrix College, 2011) e Elizabeth Patitsas (University of Toronto, 2012) — obra original em [toves.org/books/cpy/](http://www.toves.org/books/cpy/), licenciada sob [CC BY-SA 3.0 US](http://creativecommons.org/licenses/by-sa/3.0/us/). Esta versão traduz o conteúdo para português e amplia com comparações e exercícios de mapeamento **Python/VisuAlg → C**, listas de exercícios por capítulo, e capítulos novos (Vetores e Strings, Structs, Ponteiros e Alocação Dinâmica). Por ser uma obra derivada, este repositório é distribuído sob a mesma licença — veja [`LICENSE`](LICENSE).

O ponto de partida imediato deste projeto foi [profsergiocosta/cpy-ptbr](https://github.com/profsergiocosta/cpy-ptbr), uma tradução para português dos três primeiros capítulos da obra original.

## Rodando localmente

```bash
python3 -m venv .venv
source .venv/bin/activate      # no Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Depois abra `http://127.0.0.1:8000` no navegador. O `mkdocs serve` recarrega automaticamente a cada alteração nos arquivos de `docs/`.

## Publicando no GitHub Pages

1. Crie um repositório no GitHub e suba este projeto:

   ```bash
   git init
   git add .
   git commit -m "Site inicial"
   git branch -M main
   git remote add origin https://github.com/SEU-USUARIO/SEU-REPOSITORIO.git
   git push -u origin main
   ```

2. Em `mkdocs.yml`, descomente e ajuste `site_url`, `repo_url` e `repo_name` com o seu usuário/repositório.

3. O workflow em `.github/workflows/deploy.yml` já está configurado: a cada `push` na branch `main`, ele builda o site com MkDocs Material e publica na branch `gh-deploy` (`gh-pages`, criada automaticamente pelo comando `mkdocs gh-deploy`).

4. No GitHub, vá em **Settings → Pages** e, em "Source", selecione a branch `gh-pages` (pasta `/ (root)`). Depois do primeiro workflow rodar com sucesso, o site fica disponível em `https://SEU-USUARIO.github.io/SEU-REPOSITORIO/`.

Se preferir publicar manualmente sem depender do Actions, também funciona rodar localmente:

```bash
mkdocs gh-deploy --force
```

## Estrutura

```
docs/
  index.md   # página inicial
  cap01.md   # 1. Construindo um simples programa
  cap02.md   # 2. Expressões e comandos
  cap03.md   # 3. Bibliotecas
  cap04.md   # 4. Vetores e Strings
  cap05.md   # 5. Structs (registros)
  cap06.md   # 6. Ponteiros e Alocação Dinâmica
  src/       # fontes C soltos usados como exemplo no texto
mkdocs.yml
requirements.txt
LICENSE
.github/workflows/deploy.yml
```

## Sobre a abordagem pedagógica

Este material assume que quem está lendo já sabe programar em Python e/ou VisuAlg, e ensina C por **mapeamento**: cada construção da linguagem (declaração de variável, `if`, laços, funções...) é apresentada lado a lado com o equivalente em Python e em VisuAlg, e há caixas de "Exercício de mapeamento" pedindo para o leitor escrever, em C, o equivalente de um trecho já pronto em Python/VisuAlg. A recomendação para quem for usar IA como apoio nos estudos é sempre tentar resolver o mapeamento primeiro, e só depois discutir com a IA formas mais idiomáticas de escrever a mesma solução em C — mais detalhes na [página inicial do site](docs/index.md).
