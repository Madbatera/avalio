# Avalio — Gestão Clínica & Avaliação Fonológica com IA

Software para fonoaudiólogos que apoia a aplicação e a correção da
**Prova de Fonologia do ABFW** (Andrade, Béfi-Lopes, Fernandes & Wertzner —
*Teste de Linguagem Infantil nas Áreas de Fonologia, Vocabulário, Fluência e
Pragmática*), com gravação e **transcrição automática de áudio por IA**.

## O que o app faz

- Conduz o protocolo de nomeação do ABFW (34 palavras-alvo, Anexo 2).
- Grava o áudio da criança (`getUserMedia`/`MediaRecorder`) direto no
  navegador — por gravação ao vivo ou importação de arquivo.
- Transcreve automaticamente cada produção com um modelo **Whisper rodando
  localmente no navegador** ([`@xenova/transformers`](https://github.com/xenova/transformers.js),
  via WASM/WebGPU) — sem servidor, sem API key, sem enviar áudio para
  terceiros.
- Converte a transcrição em fonemas (`graphemesToPhonemes()`), consultando
  primeiro um léxico de ~9.660 palavras do português brasileiro do projeto
  [`annotator`](https://github.com/falabrasil/annotator) (Grupo FalaBrasil —
  UFPA, MIT License) e caindo para regras letra a letra quando a palavra não
  está no léxico.
- Compara a produção com a forma-alvo e classifica o desvio (acerto,
  omissão, substituição, distorção), seguindo o inventário fonético do PB.
- Aplica os critérios clínicos do manual: análise tradicional vs. análise
  de processos fonológicos, idades previstas de eliminação de cada processo,
  severidade (PCC-R/PVC-R) e **critério de produtividade** (mínimo de 4
  ocorrências **e** mais de 25% das oportunidades no protocolo — Tabela 3,
  Wertzner 1992).
- Todo resultado automático é sinalizado para **revisão do profissional**
  e pode ser editado manualmente — a transcrição por IA é um ponto de
  partida, não um laudo final.
- Mantém um "Banco de Conhecimento" de exemplos, exportável em `.zip`.

## Como rodar

É um app **single-file, 100% client-side** — sem build, sem backend.

```bash
open index.html
```

(ou sirva a pasta com qualquer servidor estático, ex. `npx serve .`, se o
navegador bloquear os `import()` de módulos ESM via `file://`).

Na primeira execução da transcrição, o navegador baixa o modelo Whisper via
CDN (`cdn.jsdelivr.net`) — é necessário acesso à internet nesse momento;
depois disso o modelo fica em cache no navegador.

## Estrutura do projeto

```
Projeto Avalia/
├── index.html            # o app (única fonte de verdade)
├── README.md
├── .gitignore
├── referencias/           # material de apoio (ex.: livro ABFW em PDF)
│   └── ...                # NÃO versionado — direitos autorais
└── dados-pacientes/        # laudos e dados reais de pacientes
    └── ...                # NÃO versionado — dado clínico sensível
```

`referencias/` e `dados-pacientes/` existem apenas localmente e estão no
`.gitignore` — nunca devem ser commitados nem publicados.

## Fundamentação clínica

Baseado no **Capítulo 1 — Fonologia (Parte A)** do livro ABFW, de Haydée
Fiszbein Wertzner: estrutura da prova de nomeação (Anexo 2) e produtividade
dos processos fonológicos por idade (Tabela 3, Wertzner 1992). A lógica de
classificação clínica (processos, idades de eliminação, severidade) é
100% baseada no manual; o léxico do FalaBrasil só melhora a etapa de
conversão ortografia → fonema.

## Histórico

O projeto começou como três protótipos HTML quase idênticos
(`avalio.html`, `avalio_1.html`, `Avalio_corrigido_html.html`). Em
2026-08-24 o repositório git foi criado e a versão mais recente e completa
(`Avalio_corrigido_html.html`, 19/08) foi consolidada como `index.html`;
as duas versões anteriores permanecem disponíveis no histórico do git.

## Aviso

Transcrição e classificação automáticas são um apoio, não um diagnóstico.
Reconhecedores de fala genéricos tendem a "corrigir" a produção para a
palavra-alvo em vez de capturar o desvio real da criança — por isso todo
resultado deve ser revisado e, se necessário, ajustado pelo profissional
antes de compor o laudo.
