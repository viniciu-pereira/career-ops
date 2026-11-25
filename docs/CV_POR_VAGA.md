# Currículo por vaga (pt-BR)

Um fluxo curto para gerar, para cada vaga, uma versão do currículo no layout "Clássico PT-BR" com as palavras-chave da descrição da vaga, sem inventar experiência.

O que este fluxo acrescenta ao career-ops:

| Peça | Onde | Para quê |
|---|---|---|
| Template `classico-ptbr` | `templates/cv-template.classico-ptbr.html` | Layout de uma coluna: nome em destaque, linha "Objetivo", cargo antes da empresa, seções Resumo / Experiência / Competências / Formação / Idiomas |
| `keyword-match.mjs --keywords` | `keyword-match.mjs` | Confere uma lista de palavras-chave que você já tem (por exemplo, copiada do JobStep) contra o seu CV |
| Regras da casa de exemplo | `examples/cv-por-vaga/_custom.cv-por-vaga.md` | O workflow "cv vaga" para colar no seu `modes/_custom.md` |

## 1. Configuração (uma vez)

Os arquivos abaixo são da camada do usuário: ficam fora do git (`.gitignore`) e nenhuma atualização do career-ops os sobrescreve. **Não os commite num fork público**, eles têm telefone e e-mail.

1. `cv.md` na raiz: o seu currículo base em Markdown. É a única fonte de fatos.
2. `config/profile.yml`: copie de `config/profile.example.yml` e ajuste pelo menos:

   ```yaml
   candidate:
     full_name: "Seu Nome"
     title: "Analista de Infraestrutura | DevOps"
   language:
     output: pt-BR
     modes_dir: modes/pt
   page_format: a4
   cv:
     template: classico-ptbr
   ```

3. `modes/_profile.md`: seus arquétipos (por exemplo DevOps, Analista de Infraestrutura, Suporte N3/NOC) e quais experiências destacar em cada um.
4. `modes/_custom.md`: copie de `modes/_custom.template.md` e cole o bloco de `examples/cv-por-vaga/_custom.cv-por-vaga.md`.
5. Confira com `node doctor.mjs --json`.

## 2. Para cada vaga

1. **Pegue as palavras-chave (opcional).** O [JobStep](https://www.jobstep.io/en) não tem API pública: é um app web com login, e o "Tailor Resume to Jobs" é recurso do plano pago. Então o uso é manual: cole a vaga lá, copie a lista de palavras-chave que ele mostrar e salve em `jds/{empresa}-{cargo}.keywords.txt`, uma por linha. Sem JobStep o fluxo funciona igual, extraindo as palavras-chave do texto da vaga.
2. **Salve a vaga** em `jds/{empresa}-{cargo}.md` (texto completo, colado).
3. **Veja a cobertura do CV base:**

   ```bash
   node keyword-match.mjs --keywords jds/acme-devops.keywords.txt
   # ou, sem lista:
   node keyword-match.mjs --jd-text jds/acme-devops.md
   ```

   A saída separa o que o seu `cv.md` já cobre do que falta. "Falta" não quer dizer que você não sabe: pode estar escrito com outras palavras.
4. **Gere o currículo** no Claude Code (ou outro CLI suportado), na raiz do repositório:

   ```text
   /career-ops pdf
   cv vaga: jds/acme-devops.md, palavras-chave em jds/acme-devops.keywords.txt
   ```

   O modo `pdf` reescreve o resumo, reordena os bullets pela relevância para a vaga, ajusta a linha "Objetivo" ao título da vaga e encaixa as palavras-chave **somente onde o `cv.md` sustenta**. Ele lista as lacunas reais antes de gerar, e você decide: se você tem a experiência, adicione-a primeiro ao `cv.md`; se não tem, ela fica de fora.
5. **Confira o resultado** em `output/`:

   ```bash
   node keyword-match.mjs --keywords jds/acme-devops.keywords.txt --cv output/<arquivo>.html
   ```

6. **Envie você mesmo.** O career-ops nunca envia candidatura.

## Regra de ouro

Palavras-chave são reescritas, nunca inventadas. Reordenar, enfatizar e usar o vocabulário da vaga para algo que você fez é o objetivo; colocar uma ferramenta que você nunca usou não é, e costuma aparecer na entrevista técnica.
