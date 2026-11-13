# atende-academia

**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

[![Atende Academia](guia/assets/banner.jpg)](https://inematds.github.io/atende-academia/guia/)

## ðŸ“– Guia de uso

Landing e guia passo a passo (PT/EN/ES): **https://inematds.github.io/atende-academia/guia/** Â· [English](https://inematds.github.io/atende-academia/guia/en/) Â· [EspaÃ±ol](https://inematds.github.io/atende-academia/guia/es/)

ðŸŽ¬ VÃ­deo: https://www.youtube.com/watch?v=QTVyy0F8ZSU

Atendimento ao aluno de uma academia ou estÃºdio pequeno, em Python 3 sÃ³ com biblioteca padrÃ£o (HTTP + SQLite): alunos e planos, aulas coletivas com reserva e lista de espera, check-in e reativaÃ§Ã£o, ficha de treino, **explicaÃ§Ã£o animada de exercÃ­cios**, avaliaÃ§Ã£o fÃ­sica, conversa por chat web e WhatsApp (Evolution API), equipe pelo Telegram, LGPD e exportaÃ§Ã£o para o Painel de RecuperaÃ§Ã£o do Raio-X de Margem.

**Estado (19/10/2026): v0.11.0 â€” implementaÃ§Ã£o completa; 99/99 testes, `LIMITES OK`. Telegram, pÃ¡ginas `/` e `/equipe`, Docker e `tools/render-exercicios` entregues.** Ver "Como rodar no seu ambiente".

## O que o sistema faz (v1)

- **Alunos e planos:** cadastro com cÃ³digo de check-in; planos mensal/trimestral/anual/aulas avulsas com preÃ§o e vigÃªncia; matrÃ­cula com status `ativa`, `vencida`, `trancada`, `cancelada`; cobranÃ§a sÃ³ como **status manual** (`pago`/`pendente`) e aviso de vencimento.
- **Aulas coletivas:** grade semanal (modalidade, professor, sala, vagas); reserva com limite de vagas e antecedÃªncia; cancelamento com prazo; lista de espera com oferta automÃ¡tica; falta automÃ¡tica (reserva sem check-in); Ãºltima vaga disputada â†’ um 201, o resto 409.
- **Check-in e frequÃªncia:** por cÃ³digo na recepÃ§Ã£o ou pelo chat ("cheguei"); aluno sem check-in hÃ¡ N dias recebe reativaÃ§Ã£o (sÃ³ com consentimento); frequÃªncia e nÃºmeros do mÃªs para o Raio-X.
- **Treino:** professor monta a ficha com exercÃ­cios da biblioteca (sÃ©ries, repetiÃ§Ãµes, carga, descanso); o aluno pede "meu treino de hoje"; qualquer exercÃ­cio do catÃ¡logo tem **animaÃ§Ã£o SVG** (`/exercicios/<id>`) e, no WhatsApp, MP4 gerado pelo HyperFrames quando existir.
- **AvaliaÃ§Ã£o fÃ­sica** agendada com o professor; resultado Ã© dado sensÃ­vel (sÃ³ equipe, nunca pela conversa).
- **Conversa:** aulas, reservar/cancelar, meu plano, meu treino, explicar exercÃ­cio, FAQ, atendente humano, alerta de saÃºde (192), "PARAR".
- **Equipe:** pÃ¡gina `/equipe` com token e Telegram (`/aulas [data]`, `/fila`, `/responder`, `/encerrar`, avisos de reserva e cancelamento).
- **Campanhas** sÃ³ com consentimento e trava mÃ­nima de publicidade; **LGPD** (exportar, anonimizar); **backup/restauraÃ§Ã£o**; **Docker** para VPS.

Contrato completo: `docs/ESPECIFICACAO.md` (22 seÃ§Ãµes). Fora do v1: seÃ§Ã£o 16.

## Como rodar no seu ambiente

O sistema estÃ¡ **implementado**: v0.11.0, 99/99 testes, `LIMITES OK` e verificaÃ§Ã£o independente aprovada. Qualquer pessoa pode rodar no prÃ³prio ambiente.

### PrÃ©-requisitos

- Linux/macOS com `git` e `python3` â‰¥ 3.10. A aplicaÃ§Ã£o nÃ£o tem dependÃªncias; `pytest` (`pip install pytest`) sÃ³ serve para conferir os testes.
- Para o deploy: `docker` (com `docker compose`).
- Opcional, para os MP4 dos exercÃ­cios: Node 22 + HyperFrames + `ffprobe`. O `tools/render-exercicios` e o verificador sÃ³ usam o HyperFrames jÃ¡ instalado (`npx --no-install hyperframes`); para instalar uma vez: `npm install hyperframes` na raiz do repo (`node_modules/`, `package.json` e `package-lock.json` estÃ£o no `.gitignore`, entÃ£o o `verificar-limites.sh` nÃ£o os vÃª) ou `npm install -g hyperframes`.

### 1. Clonar e conferir

```sh
git clone https://github.com/inematds/atende-academia && cd atende-academia
python3 -m pytest -q tests/ | tail -n 1                          # 99 passed
bash longrun/2026-10-05-academia-v1/verificar-limites.sh          # LIMITES OK no fechamento; hoje acusa sÃ³ guia/ e README.en/es (docs posteriores)
```

`LIMITES OK` = hash do contrato intacto, nÂº de testes igual, sÃ³ arquivos do escopo, sem dependÃªncia externa, sem URL externa no cÃ³digo, sem chave, sem `0.0.0.0` no cÃ³digo, nada de MP4/`node_modules` commitado.

### 2. Executar

```sh
mkdir -p dados && cp exemplos/academia.json dados/academia.json   # edite nome, token, grade, planos
./atende serve --porta 8080 --dados dados
./atende raiox --dados dados --mes 2026-10 --acompanhamento acompanhamento.json
./atende backup --dados dados --saida backups/academia.zip
```

Chat do aluno em `/`, equipe em `/equipe` (token de `academia.json` no header `X-Token`), animaÃ§Ã£o de cada exercÃ­cio em `/exercicios/<id>`. `PUBLIC_URL` (endereÃ§o pÃºblico, HTTPS, sem barra final) entra nos links enviados ao aluno; sem ela os links sÃ£o relativos. Sem as variÃ¡veis da Evolution e do Telegram, a caixa de saÃ­da Ã© simulada e nenhuma chamada de rede sai. As 20 decisÃµes padrÃ£o estÃ£o em `docs/DECISOES-ABERTAS.md`.

**MP4 dos exercÃ­cios (WhatsApp nÃ£o mostra SVG):** `tools/render-exercicios [--ids a,b] [--saida DIR]` gera `web/exercicios/<id>.mp4` (720Ã—720, 4 s em loop, sem Ã¡udio) a partir de cada SVG com o HyperFrames â€” render local por Chromium, sem API nem conta. Precisa de Node 22+, HyperFrames instalado (`npm install hyperframes` na raiz; o script nÃ£o baixa nada sozinho) e `ffprobe`. A composiÃ§Ã£o Ã© **sÃ³ CSS** (o SVG inline dentro de `data-width="720" data-height="720" data-duration="4"`, sem GSAP nem CDN): o render busca cada quadro pelos adaptadores CSS/WAAPI, por isso os SVGs usam `@keyframes` com duraÃ§Ãµes de 1, 2 ou 4 s e nunca SMIL. Ã‰ um passo de build: roda uma vez na mÃ¡quina de quem publica, nÃ£o no servidor nem nos testes. **Este render ainda nÃ£o foi rodado** (as animaÃ§Ãµes SVG funcionam na web). Com o MP4 presente e `PUBLIC_URL` definida, o bot envia o vÃ­deo por `sendMedia`; senÃ£o manda o link da pÃ¡gina.

### 3. VerificaÃ§Ã£o independente (nÃ­vel 4)

```sh
python3 longrun/2026-10-05-academia-v1/verificar-independente.py   # INDEPENDENTE OK
```

Procura valores do fixture copiados no cÃ³digo; sobe uma academia **nunca vista** (outra grade, planos, prazos, feriado, relÃ³gio) e confere regras que sÃ³ passam se a lÃ³gica for geral; abre 3 SVGs num Chromium headless e exige que dois quadros em instantes diferentes sejam diferentes (prova de movimento; sem Chromium que passe na calibraÃ§Ã£o â†’ `PULADO`); se o HyperFrames estiver instalado, renderiza um exercÃ­cio com `tools/render-exercicios` e confere 720Ã—720, ~4 s, sem Ã¡udio com `ffprobe` (senÃ£o â†’ `PULADO`); faz `docker build` e testa o healthcheck do container (sem `docker` â†’ `PULADO`). Na entrega saiu `INDEPENDENTE OK (movimento: chromium, hyperframes: pulado, docker: ok)`. Depois abra `/`, `/equipe` e `/exercicios/prancha` no navegador.

### 4. Como foi construÃ­do (para refazer ou adaptar)

O sistema foi construÃ­do pelo mÃ©todo de execuÃ§Ã£o longa (`~/projetos/execucao-longa`: `tools/loop-longrun.sh`, `docs/PLANO-EXECUCAO-LONGA.md`), por um agente pela **assinatura** (Codex CLI e Claude Code, nenhuma API chamada):

1. As decisÃµes de `docs/DECISOES-ABERTAS.md` foram respondidas e o contrato foi congelado com `bash longrun/2026-10-05-academia-v1/congelar.sh` (grava `hash-congelado.txt`). Contrato, testes e os dois verificadores ficaram protegidos: o agente nÃ£o os alterava; teste em conflito com a spec ia para `failures.md` (portÃ£o humano).
2. O agente trabalhou em loop headless (`~/projetos/execucao-longa/tools/loop-longrun.sh longrun/2026-10-05-academia-v1`, Codex `gpt-6-astra`, configuraÃ§Ã£o em `loop.env`: 20 ciclos Ã— 30 min, 8 GB, parada apÃ³s 3 ciclos sem avanÃ§o, `flock`, `timeout`, `MemoryMax`, reversÃ£o de ciclo que mexesse em arquivo protegido, checkpoint por ciclo) e foi concluÃ­do pelo Claude Code, atÃ© **99 passed** e **LIMITES OK**.
3. O acompanhamento ficou em `longrun/2026-10-05-academia-v1/` (`loop.log`, `state.md`, `progress.md`).

Quer mudar uma regra marcada âš  ou levar o molde para outro negÃ³cio? Edite `docs/ESPECIFICACAO.md` e os testes, congele de novo e rode o loop headless, ou o `/goal` no Codex TUI (`codex -c sandbox_workspace_write.network_access=true` + `longrun/2026-10-05-academia-v1/prompt-goal-codex.md`) ou no Claude Code (`longrun/2026-10-05-academia-v1/prompt-goal-claude.md`).

## Deploy na VPS

Passo do dono, com as credenciais e a mÃ¡quina dele (o sistema nunca faz isso sozinho).

1. **Subir o compose.** Na VPS, com Docker: clone o repo, `cp .env.exemplo .env`, preencha as variÃ¡veis e rode `docker compose up -d --build`. O serviÃ§o `atende` escuta sÃ³ em `127.0.0.1:8080`, guarda tudo em `./dados` (volume `/dados`) e tem healthcheck em `/api/saude`. Na primeira subida, `dados/academia.json` Ã© copiado do exemplo: edite nome, **token**, grade e planos e reinicie (`docker compose restart`).
2. **Proxy HTTPS.** Coloque um proxy reverso (Caddy, Nginx ou Traefik) com certificado na frente de `127.0.0.1:8080`, no domÃ­nio da academia. Nunca publique a porta 8080 direto.
3. **`PUBLIC_URL`.** No `.env`, `PUBLIC_URL` = o endereÃ§o HTTPS do domÃ­nio, sem barra final. Ã‰ o que entra nos links de exercÃ­cio e no `sendMedia` do MP4.
4. **Webhook da Evolution.** Preencha `EVOLUTION_URL` (endereÃ§o interno da Evolution), `EVOLUTION_API_KEY`, `EVOLUTION_INSTANCIA` e um `WEBHOOK_SEGREDO` longo e aleatÃ³rio. No painel da Evolution, aponte o webhook da instÃ¢ncia para `<PUBLIC_URL>/webhook/evolution/<WEBHOOK_SEGREDO>` com o evento `MESSAGES_UPSERT`. Segredo errado responde 404.
5. **Webhook do Telegram.** Crie o bot no BotFather, coloque-o no grupo da equipe e preencha `TELEGRAM_API_URL` (endereÃ§o da API do Telegram), `TELEGRAM_TOKEN`, `TELEGRAM_CHAT_ID` (id do grupo) e `TELEGRAM_SEGREDO`. Registre o webhook chamando `setWebhook` da API do bot com `url=<PUBLIC_URL>/webhook/telegram` e `secret_token=<TELEGRAM_SEGREDO>` â€” o sistema recusa (401) update sem esse cabeÃ§alho e ignora outros chats. No grupo: responder em cima da mensagem `#id`, `/responder <id> <texto>`, `/encerrar <id>`, `/fila`, `/aulas [AAAA-MM-DD]`.
6. **Gerar os MP4.** Numa mÃ¡quina com Node 22+, `npm install hyperframes` e `ffprobe`, rode `tools/render-exercicios` (ou `--ids prancha,agachamento --saida DIR`) e copie os `.mp4` para a pasta apontada por `EXERCICIOS_MP4_DIR` (ex.: `/dados/mp4` dentro do container = `./dados/mp4` na VPS). Sem MP4 o WhatsApp recebe o link da pÃ¡gina animada.
7. **Backup diÃ¡rio.** No cron do host, por exemplo Ã s 3 h: `docker compose exec -T atende ./atende backup --dados /dados --saida /dados/backups/academia-$(date +\%F).zip` (o `./atende backup` pode rodar com o servidor ligado). Para restaurar: pare o serviÃ§o, rode `./atende restaurar --dados /dados --entrada <arquivo>` e suba de novo. Guarde cÃ³pias fora da VPS.

## Arquivos

- `docs/ESPECIFICACAO.md` â€” contrato (22 seÃ§Ãµes, tabela de rotas, regras com fronteiras, fora do v1).
- `docs/DECISOES-ABERTAS.md` â€” decisÃµes com proposta padrÃ£o; perguntas para o dono.
- `docs/MAPA-RAIO-X.md` â€” cada vazamento do pacote `servicos` â†’ peÃ§a do v1 ou motivo de ficar fora.
- `tests/` â€” 99 testes caixa-preta (`conftest.py` sobe o CLI, relÃ³gio fixo, servidor falso de Evolution/Telegram, pasta temporÃ¡ria de MP4). Fixture em `tests/fixtures/academia.json`; cÃ³pia em `exemplos/`.
- `longrun/2026-10-05-academia-v1/` â€” goal (nÃ­vel 3+), plan, state, loop.env, prompts, `congelar.sh`, `verificar-limites.sh`, `verificar-independente.py`.
- `FALHAS.md` â€” uma linha por falha (data, o que quebrou, menor correÃ§Ã£o, prompt | infra).

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe