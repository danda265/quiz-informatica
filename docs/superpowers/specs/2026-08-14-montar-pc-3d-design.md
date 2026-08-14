# Monte seu PC — pivô para 3D real

**Data:** 2026-08-14
**Projeto:** quiz-informatica
**Status:** aprovado para implementação
**Substitui:** as seções técnicas/visuais de `docs/superpowers/specs/2026-08-12-montar-pc-design.md` (§3 Arquitetura, §5 Mecânica de arrastar e soltar, partes visuais de §7 e §9). **As demais seções desse spec original continuam valendo** — ver §1 abaixo.

## 1. Por que o pivô, e o que continua igual

Depois da spec original (2026-08-12) e das Tasks 1-3 da implementação 2D (SVG/CSS, motor de arrastar/soltar) já estarem prontas, testadas e aprovadas, o usuário viu [buildcores.com](https://pt.buildcores.com) — um configurador de PC em 3D de verdade (WebGL, modelos licenciados de fabricantes) — e decidiu que quer 3D real em vez do estilo ilustrado 2D, mesmo sabendo que isso descarta o trabalho 2D já feito (mantido intacto na branch `feature/monte-seu-pc`, não apagado, apenas não usado).

**O que este documento muda:** só a camada de renderização e interação (como as peças são desenhadas e como o aluno as encaixa).

**O que continua exatamente como no spec original (2026-08-12), sem mudança:**
- As 14 peças/cabos, quais telas existem, o que cada peça faz (§4 do spec original, tabelas das Telas 1 e 2)
- Tela de aviso de eletricidade estática antes de começar (§4, Tela 0)
- Duas etapas: bancada (placa-mãe fora do gabinete) → gabinete
- Dica visual após alguns segundos parado, ordem livre com sugestão (§5, adaptado só na forma de destacar o alvo — ver §4 abaixo)
- Validação em camadas ao clicar "Ligar", um problema por vez, mesma ordem e mesmas mensagens (§6)
- GPU opcional, painel frontal como conector único (§2, §11 "fora de discussão")
- Sem Firebase, sem rastreio, sem admin.html (§2, §3)
- Resumo final com as 14 peças na tela de sucesso (§7)
- Card de entrada pelo `index.html` (mesmo card "🛠️ Monte seu PC")

## 2. Arquitetura

**Continua um arquivo único `montar-pc.html`**, mas agora numa branch/worktree nova (`feature/monte-seu-pc-3d`), separada da branch 2D (`feature/monte-seu-pc`, preservada intacta como referência/backup, não deletada).

**Three.js via CDN** (`<script type="importmap">` + `import` de `three` e `OrbitControls`, direto de um CDN como unpkg/jsdelivr) — sem npm, sem bundler, sem build step, mesma filosofia "arquivo estático" do resto do site. É a única dependência externa nova do projeto (o resto do site só usa o SDK do Firebase, também via CDN, em `index.html`).

**Duas cenas 3D separadas**, uma por etapa (bancada e gabinete), trocadas exatamente como as telas HTML eram trocadas no design 2D — ao concluir a bancada, a cena da Etapa 2 recebe a placa-mãe (com o que foi instalado nela) como um objeto já pronto.

**Bandeja de peças continua em HTML/CSS**, fora do canvas 3D — ícones simples com nome, clicáveis para "selecionar" a peça. O canvas 3D mostra só o objeto em construção (placa-mãe ou gabinete). Decisão deliberada: manter a complexidade 3D restrita ao objeto sendo montado, não à interface de seleção — mantém a bandeja simples, responsiva e com rótulo em texto (importante para a idade do público).

## 3. Peças 3D

Cada peça é composta de formas geométricas básicas do Three.js (`BoxGeometry`, `CylinderGeometry`, etc.) combinadas em um grupo, com cor e material próprios — sem foto, sem modelo 3D externo baixado/gerado (evita risco de direitos autorais e mantém peso leve, mesma decisão de princípio do spec original, só que agora em 3D em vez de SVG). Ex.: CPU = caixa escura + quadrado dourado nos contatos; cooler = cilindro + pás; RAM = placa fina vertical com trilha dourada de conector.

**Acabamento "com estúdio de produto"** (nível intermediário, escolhido entre 3 opções apresentadas): material que reage à luz de forma realista (`MeshStandardMaterial`, metal parece metal, plástico parece plástico), sombras suaves só nas peças principais (não no cenário todo, para não pesar), 2-3 fontes de luz (ambiente + direcional principal + preenchimento) — equilíbrio entre parecer com a referência do BuildCores e rodar bem em Chromebook de escola.

## 4. Interação: clicar peça → clicar local

**Sem arrastar de verdade em 3D** — decisão explícita após avaliar o risco: arrastar em profundidade dentro de uma cena 3D é tecnicamente mais complexo (raycasting contínuo) e o toque em celular fica impreciso, risco real de frustração para o público de 5º-9º ano.

Fluxo: aluno toca/clica numa peça da bandeja HTML (ela fica destacada, "armada"); depois toca/clica num ponto do modelo 3D. Um raycaster do Three.js detecta em qual "soquete" invisível (um volume 3D posicionado no modelo, equivalente às `drop-zone` do design 2D) o clique caiu.
- Soquete certo → peça se ajusta visualmente na posição, trava, mostra a mesma confirmação de texto de antes.
- Soquete errado (ou nenhum) → mesma mensagem de erro de antes ("Essa não é o lugar certo — aqui vai: X"), com algum feedback visual de rejeição (ex: um leve tremor/flash na peça ainda não posicionada).

**Câmera orbital livre** (`OrbitControls`): aluno gira a câmera arrastando (mouse) ou com o dedo (toque), dá zoom com a roda/pinça, para ver o modelo de qualquer ângulo antes de escolher onde encaixar.

**Cooler continua dependente da CPU**: o soquete do cooler só existe/fica clicável depois que a CPU está instalada — mesma regra de exceção à ordem livre do spec original, agora natural em 3D (não há soquete de cooler visível/raycastável antes da CPU).

**Dica por inatividade**: em vez de um "glow" pulsante numa `drop-zone` HTML, o soquete sugerido pisca/destaca visualmente dentro da cena 3D (ex: contorno ou pulso de emissão no material do soquete-alvo) após o mesmo período de inatividade de antes.

## 5. Tela de sucesso

Durante a montagem a câmera é livre; ao clicar "Ligar" com tudo certo, a câmera **anima suavemente até um ângulo fixo "de capa"** (gabinete de frente) para o momento de celebração — LEDs do painel piscando em verde (emissive material animado), ventoinha girando de verdade (rotação do objeto 3D). Confete/balões e a figurinha "Missão Concluída" continuam como camada HTML por cima do canvas 3D (não precisam ser reconstruídos em 3D).

## 6. Fora de escopo (mesmas exclusões do spec original, reafirmadas)

- Sem fotos reais nem modelos 3D baixados/gerados de terceiros — só geometria própria
- Sem Firebase/rastreio, sem mudança em `admin.html`
- Sem pinos individuais no painel frontal (continua 1 conector único)
- Sem ordem de montagem obrigatória (livre, com sugestão) — única exceção: cooler após CPU
- Sem build tool/bundler — Three.js via CDN, arquivo único

## 7. Pendência (herdada do spec original)

Mascote "Missão Concluída" (`assets/missao-concluida.png`) já existe no repositório (branch 2D) — precisa ser copiado/recriado na nova branch 3D antes da tela de sucesso ser implementada.
