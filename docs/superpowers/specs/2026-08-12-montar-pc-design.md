# Monte seu PC — simulação de montagem interativa

**Data:** 2026-08-12
**Projeto:** quiz-informatica
**Status:** aprovado para implementação

## 1. O que é

Nova atividade interativa dentro do quiz-informatica: o aluno monta um computador do zero, peça por peça, arrastando componentes até o lugar certo — do processador aos cabos da fonte — e no final tenta ligar o PC. Se faltar algo, o jogo avisa o que está errado, um problema de cada vez, até o aluno acertar tudo e o computador ligar de verdade (com animação de comemoração).

Complementa o Módulo 1 "Hardware" do quiz (que é só múltipla escolha) com uma prática mão-na-massa: onde cada peça entra e para que ela serve.

## 2. Escopo

**Dentro do escopo (v1):**
- Simulação completa de montagem: 14 peças/cabos, em 2 etapas (bancada da placa-mãe → gabinete)
- Aviso de segurança (eletricidade estática) antes de começar
- Arrastar e soltar com feedback imediato (encaixe trava / erro treme e volta)
- Dica visual por inatividade (sem bloquear ordem livre)
- Botão "Ligar" com validação em camadas, um problema por vez, do mais crítico ao mais sutil
- Tela de sucesso com mudança de ângulo (gabinete em pé), luzes, confete, mascote e resumo educativo
- Botão de reiniciar a montagem

**Fora do escopo (v1 — não fazer):**
- Salvar progresso/conclusão no Firebase ou aparecer no `admin.html` (é prática livre, sem rastreio)
- Ordem de montagem obrigatória/bloqueada (é livre, com sugestão visual)
- Fotos reais de hardware (usa ilustração desenhada, por direitos autorais e consistência visual)
- Pinos individuais do painel frontal (vira 1 conector único simplificado)
- Modelos 3D giráveis / WebGL (visual "realista" é ilustração 2D/2.5D com sombra e profundidade, não 3D de verdade)

## 3. Arquitetura

Arquivo novo e separado: `montar-pc.html`, no mesmo padrão de `index.html` / `admin.html` / `ranking.html` (HTML+CSS+JS vanilla num arquivo só, sem build tool, sem framework, deploy direto no GitHub Pages).

Motivo: mantém `index.html` (hoje 680 linhas) sem inchar com um motor de drag-and-drop completamente diferente do motor de quiz. Cada arquivo com uma responsabilidade clara.

**Integração com o quiz existente:** `index.html` ganha um novo card na grade de módulos (`#mod-grid`), visualmente diferente dos módulos numerados (ex: sem "Módulo N", com destaque próprio), que quando clicado navega para `montar-pc.html`. Nenhuma outra mudança em `index.html`.

**Sem Firebase:** `montar-pc.html` não inclui os SDKs do Firebase nem salva nada — roda inteiramente client-side.

**Estilo visual:** reaproveita a paleta de cores e tipografia do `index.html` (tokens como `--bg`, `--card`, `--text`, `--muted`, `--border`, família de fontes `Segoe UI`) copiados diretamente no `<style>` do novo arquivo, para manter a identidade visual do site sem depender de um CSS compartilhado externo.

## 4. Fluxo de telas

```
Tela 0: Aviso de segurança
   ↓ "Entendi, vamos montar →"
Tela 1: Bancada (placa-mãe fora do gabinete)
   ↓ "Levar pro gabinete →" (disponível a qualquer momento, ordem livre)
Tela 2: Gabinete deitado, aberto, vazio
   ↓ botão "⚡ Ligar" (disponível a qualquer momento)
Tela 3: Resultado
   - se faltar algo → mostra 1 problema (o mais crítico), botão "Voltar e corrigir"
   - se tudo certo → gabinete em pé de frente, luzes/fans, confete, mascote, resumo
   ↓ "Montar de novo" (sempre disponível, volta pra Tela 0)
```

### Tela 0 — Aviso de segurança

Card explicando, em linguagem simples para 5º-9º ano:
- Descarregar eletricidade estática antes de tocar nas peças (encostar em metal aterrado/no próprio gabinete)
- Segurar as peças pelas bordas — nunca tocar nos contatos dourados/pinos
- Não montar sobre carpete/tecido
- Cuidado com as quinas do gabinete

Botão único: "Entendi, vamos montar →".

### Tela 1 — Bancada

A placa-mãe aparece sozinha, "sobre a mesa", fora de qualquer gabinete. Bandeja com 4 peças abaixo:

| # | Peça | Onde encaixa | Para que serve (texto do card) |
|---|------|---------------|----------------------------------|
| 1 | Processador (CPU) | Soquete da placa-mãe | "O processador executa as instruções dos programas — é o 'cérebro' do computador." |
| 2 | Cooler | Sobre a CPU, preso por clipes | "Resfria o processador para ele não superaquecer enquanto trabalha." |
| 3 | Memória RAM | Slots DIMM | "Guarda temporariamente os dados que o processador está usando no momento." |
| 4 | SSD M.2 | Slot M.2 da placa-mãe | "Armazena o sistema operacional e os arquivos de forma permanente e rápida." |

Botão "Levar pro gabinete →" sempre visível/habilitado (não bloqueia mesmo com peças faltando — a validação de verdade acontece na hora de ligar).

### Tela 2 — Gabinete deitado

Gabinete visto de cima/aberto, **completamente vazio no início**. Bandeja com as peças restantes (a placa-mãe montada na Tela 1 é a primeira peça da bandeja aqui):

| # | Peça | Onde encaixa | Para que serve |
|---|------|---------------|------------------|
| 5 | Placa-mãe (montada) | Bandeja/parafusos do gabinete | "Placa principal que conecta todos os componentes do computador." |
| 6 | Fonte (PSU) | Baia da fonte | "Converte a energia da tomada para a energia que cada peça precisa." |
| 7 | HD/SSD SATA extra | Baia de drive | "Armazenamento adicional para arquivos, fotos e programas." |
| 8 | Placa de vídeo (GPU) — **opcional** | Slot PCIe | "Processa gráficos e imagens — opcional, o vídeo pode sair direto da placa-mãe (vídeo integrado)." |
| 9 | Cabo 24-pin | Fonte → placa-mãe | "Leva a energia principal da fonte para a placa-mãe." |
| 10 | Cabo CPU 4/8-pin | Fonte → placa-mãe (perto do soquete) | "Leva energia extra dedicada ao processador." |
| 11 | Cabo SATA de dados | Placa-mãe → HD/SSD | "Transporta os dados entre o armazenamento e o resto do PC." |
| 12 | Cabo SATA de energia | Fonte → HD/SSD | "Leva energia da fonte até o HD/SSD extra." |
| 13 | Cabo do painel frontal (conector único) | Bloco de pinos da placa-mãe | "Conecta o botão liga/desliga, o botão de reset e as luzes do gabinete à placa-mãe." |
| 14 | Cabo de vídeo (HDMI) | GPU (se instalada) ou saída de vídeo integrada da placa-mãe | "Leva a imagem até o monitor — sai da GPU se ela existir, senão sai direto da placa-mãe." |

Botão "⚡ Ligar" sempre visível/habilitado.

## 5. Mecânica de arrastar e soltar

- Implementado com **Pointer Events** (`pointerdown`/`pointermove`/`pointerup`), unificando mouse e toque — mesma técnica validada no protótipo interativo testado durante o brainstorming.
- **Encaixe correto:** peça se ajusta visualmente ao slot e trava lá (não pode mais ser arrastada); aparece um card curto de confirmação com o texto "para que serve" daquela peça (mesmo texto da tabela acima).
- **Encaixe errado:** peça balança (animação de "shake") e volta sozinha para a bandeja; mensagem curta explica por que aquele não é o lugar (ex.: "Essa é a baia do SSD, não o slot de RAM").
- **Dica por inatividade:** se o aluno ficar ~8-10 segundos sem soltar nenhuma peça, o slot da **próxima peça sugerida** (primeira ainda vazia, na ordem da tabela) pisca com um destaque visual (glow pulsante), sem texto forçado. É só sugestão — o aluno pode ignorar e tentar outra peça, a ordem é livre.

## 6. Botão "Ligar" — validação em camadas

Ao clicar em "⚡ Ligar", o jogo percorre esta lista **em ordem** e mostra **só o primeiro problema encontrado** (não a lista inteira):

1. Cabo do painel frontal não conectado → "Você apertou o botão, mas nada acontece — o cabo do painel frontal não está ligado, é ele que conecta o botão físico à placa-mãe."
2. Placa-mãe, CPU, cooler ou RAM ausentes → nomeia a peça essencial que falta, PC não liga
3. Fonte ausente, ou cabo 24-pin / cabo CPU-power desconectado → "Sem energia chegando na placa-mãe."
4. Nem SSD M.2 nem HD/SSD SATA instalado → PC liga (fans/luzes ativam) mas tela mostra erro estilo BIOS "Nenhum sistema operacional encontrado"
5. Cabo de vídeo não conectado (ou conectado no componente errado, ex.: GPU instalada mas cabo foi pro vídeo integrado) → PC liga, ventoinha gira, mas "o monitor fica preto"
6. HD/SSD extra instalado mas sem cabo SATA de dados e/ou energia → drive não é reconhecido, mas o resto liga

Cada tentativa resolve um problema por vez: o aluno corrige, aperta "Ligar" de novo, e assim vai vendo os avisos sumirem até o sucesso completo.

## 7. Tela de sucesso

Quando todos os itens obrigatórios (todos exceto GPU, que é opcional) estão corretos:

- A cena muda do gabinete deitado/aberto para o **gabinete em pé, de frente** (como fica numa mesa de verdade)
- LEDs do painel frontal piscam em verde, ventoinhas giram (animação CSS)
- Confete e balões caem na tela
- Aparece a figurinha "Missão Concluída!" (fornecida pelo usuário — ver Pendências)
- Resumo final: lista as 14 peças com a função de cada uma (revisão do que foi aprendido)
- Botão "Montar de novo" reinicia tudo (volta pra Tela 0)

## 8. Responsividade e acessibilidade básica

- Layout adaptável a telas pequenas, seguindo o mesmo breakpoint usado em `index.html` (`@media(max-width:520px)`)
- Arrastar funciona em toque (Pointer Events cobre touch nativamente, sem necessidade de polyfill)
- Textos dos cards de peça em linguagem simples, adequada a alunos do 5º ao 9º ano

## 9. Peças ilustradas

Ícones/ilustrações desenhados em SVG/CSS (gradiente, sombra, profundidade), estilo consistente entre todas as peças — **não são fotos reais** (evita problema de direitos autorais em site público no GitHub Pages e mantém visual uniforme). Estilo validado ao vivo durante o brainstorming (protótipo de uma memória RAM com efeito de profundidade).

## 10. Pendências antes de implementar

- **Mascote "Missão Concluída":** o usuário precisa salvar o arquivo PNG (figurinha enviada durante o brainstorming) em `quiz-informatica/assets/` (ou pasta equivalente) — não foi possível persistir o anexo do chat direto no disco durante esta sessão.

## 11. Fora de discussão nesta spec (decisões já fechadas, não reabrir)

- Sem Firebase/rastreio — decisão explícita do usuário
- Ordem livre com sugestão — não vira passo a passo bloqueado
- Arquivo separado `montar-pc.html` — não embutir no `index.html`
- Painel frontal como conector único — não simular pinos individuais
