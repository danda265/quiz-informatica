# Quiz de Informática

Quiz de Informática Básica para o Ensino Fundamental, do Prof. Anderson Rodrigo Costa: 12 módulos (Hardware, Windows, Arquivos e Pastas, Digitação, Internet e Segurança, Word, Excel, PowerPoint, Manutenção, Lógica e Programação, Nuvem e Colaboração, Projeto Final) com 10 questões cada. O aluno escolhe o módulo, responde e vê o gabarito comentado; o resultado vai para o professor (servidor de relatórios do EducaJogo).

No ar em https://quiz.educajogo.com.br/

## Páginas

- `index.html`: o quiz do aluno. Aceita `?escola=...&turma=...` (o link que o professor gera no painel).
- `admin.html`: painel do professor (precisa da senha do professor): resultados da turma, filtros, link para os alunos e relatório.
- `ranking.html`: ranking da turma para imprimir.

## Demonstração e jogo completo

O mesmo endereço abre duas versões:

- **Demonstração**, para quem não tem código de turma: os módulos 1 (Hardware) e 2 (Windows); os outros 10 aparecem com cadeado e uma tela de venda. A demonstração não pede escola e turma e não envia nada ao servidor de relatórios. `admin.html` e `ranking.html` levam para o jogo completo.
- **Jogo completo**, para quem entrou com o código da turma (serviço `educajogo-acesso`): os 12 módulos, o painel do professor e o ranking. O link que o painel gera para os alunos é o da raiz do site (`.../index.html?escola=...&turma=...`): cada aparelho abre a demonstração ou o completo conforme tenha o código.

O que é do jogo completo fica no `index.html` entre as marcas `COMPLETO` (as questões dos módulos 3 a 12). Quem monta o site (`publicar_jogo.py`, na pasta Projetos, fora deste repositório) tira esses trechos da demonstração, coloca a tela de venda, gera o completo em `_full/` e confere que nada pago ficou na demonstração. Abrindo o `index.html` direto no navegador você joga o completo, porque a tela de venda só entra na montagem do site.

## Arquivos

- `index.html`, `admin.html`, `ranking.html`: as três páginas (cada uma é um arquivo só).
- `assets/missao-concluida.png`: imagem que nenhuma das páginas usa hoje.

---
✨ Criado por Anderson Rodrigo Costa
