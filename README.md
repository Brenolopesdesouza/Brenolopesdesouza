<div align="center">

# AchievementBackup

**Backup inteligente de saves, conquistas, estatisticas e dados da Steam para Millennium**

[![Millennium](https://img.shields.io/badge/Millennium-Compatible-0ea5e9?style=for-the-badge&logo=steam)](https://steambrew.app/)
[![Python](https://img.shields.io/badge/Backend-Python-ffe800?style=for-the-badge&logo=python&logoColor=black)](https://www.python.org/)
[![Steam](https://img.shields.io/badge/Steam-Backup-171a21?style=for-the-badge&logo=steam)](https://store.steampowered.com/)
[![License](https://img.shields.io/badge/License-Source%20Available-red?style=for-the-badge)](license)

<h3>Feche o jogo. Guarde o momento. Restaure quando precisar.</h3>

AchievementBackup e um plugin para Steam/Millennium criado para proteger progresso local, conquistas, estatisticas, userdata, saves e arquivos relacionados aos jogos. Ele monitora sessoes da Steam, detecta quando jogos iniciam e fecham, e cria backups ou capturas com foco no AppID correto.

Este projeto foi construido a partir da base do **CalyRecall**, de **BruxinCore**, com alteracoes focadas em capturas por jogo, organizacao por conta Steam, restauracao mais segura, suporte a saves externos, API de locais de save e uma interface personalizada.

</div>

---

## O Que Ele Faz

| Area | Descricao |
| :--- | :--- |
| **Backups completos** | Cria backups gerais da Steam, incluindo `userdata`, `appcache/stats`, `depotcache`, `config/lua` e `stplug-in`. |
| **Capturas por jogo** | Cria snapshots especificos por AppID, com stats, conquistas/cache, userdata, saves, Lua Tools e saves externos do jogo. |
| **Capturas agrupadas** | A aba Capturas primeiro mostra os jogos; ao abrir um jogo, mostra todas as capturas dele. |
| **API Ludusavi/PCGamingWiki** | Usa o manifest do Ludusavi para encontrar caminhos reais de save no Windows e Steam. |
| **Modo Garantia de Saves** | Quando ligado, tambem inclui pastas externas que parecem ser do jogo. Quando desligado, usa apenas caminhos oficiais, Steam e API. |
| **Identificacao de modo** | Cada captura mostra se foi criada em modo `Seguro` ou `Garantia`. |
| **Restauracao assistida** | Fecha a Steam, restaura arquivos e abre a Steam novamente para evitar sobrescrita. |
| **Contas Steam identificadas** | Lista nome da conta e SteamID nos backups, capturas e detalhes. |
| **Mais informacoes** | Mostra jogos armazenados, AppIDs, categorias de arquivos, contas e quantidade de arquivos. |
| **Apps ignorados** | Permite ignorar apps de fundo da Steam, como Wallpaper Engine, para nao disparar backup automatico nem captura. |
| **Hotkey global** | Permite configurar um atalho para backup rapido. |
| **Temas de cor** | Interface com temas como vermelho, azul, verde e roxo. |
| **Portabilidade** | Exporta e importa backups em `.zip`. |

---

## Melhorias Sobre a Base CalyRecall

- Nome e interface visual como **AchievementBackup**.
- Abas **Backups**, **Capturas** e **Configuracoes**.
- Capturas separadas dos backups normais.
- Capturas agrupadas por jogo para evitar listas baguncadas.
- Backup automatico pos-jogo, disparado depois que o jogo fecha.
- Suporte a mais de uma sessao de jogo ao mesmo tempo.
- Espera de seguranca antes de copiar arquivos pos-jogo.
- Restauracao fechando a Steam completamente e reabrindo depois.
- Listagem por conta Steam com nome e ID.
- Tela de detalhes com jogos, AppIDs, contas e tipos de dados armazenados.
- Suporte a saves externos em `Documents`, `Saved Games`, `AppData`, `LocalLow`, `Roaming` e caminhos do manifest Ludusavi.
- Botao para controlar o scanner amplo de saves externos.
- Lista de apps ignorados por AppID, util para apps em segundo plano como Wallpaper Engine.
- Capturas identificam se foram criadas em modo Seguro ou Garantia.
- Atualizacao mais suave dos numeros da UI durante backups.
- Backend HTTP multithread para reduzir travamentos da interface.
- Correcoes no salvamento de hotkey e configuracoes.

---

## Como Funciona

### 1. Deteccao De Sessao

O plugin acompanha jogos em execucao pela Steam e por processos instalados. Quando um jogo inicia, ele registra a sessao daquele AppID. Quando o jogo fecha, ele encerra a sessao real e executa o fluxo configurado.

### 2. Espera Pos-Jogo

Antes de copiar arquivos, o AchievementBackup aguarda a Steam estabilizar escrita local e possivel sincronizacao da Steam Cloud. Isso reduz risco de copiar arquivos incompletos.

### 3. Backup Completo

O backup completo e geral. Ele nao representa apenas o ultimo jogo fechado; ele guarda dados importantes da Steam como um pacote amplo:

```text
backups/
  CalyBackup-2026-05-17_12-26-28/
    userdata/
    appcache_stats/
    depotcache/
    lua/
    stplug-in/
    caly_meta.json
```

### 4. Captura Por Jogo

A captura por jogo fica organizada por nome do jogo e data:

```text
backups/
  GRIS/
    2026-05-17_12-00/
      manifest.json
      stats/
      achievements/
      userdata/
      saves/
      lua-tools/
      external-saves/
```

Na interface, a aba **Capturas** agrupa tudo por jogo. Ao clicar em `GRIS`, por exemplo, aparecem apenas as capturas de GRIS.

### 5. Saves Externos

Para capturas, o plugin pode salvar arquivos fora da Steam usando:

- caminhos oficiais da Steam;
- manifest Ludusavi/PCGamingWiki;
- scanner amplo opcional pelo **Modo Garantia de Saves**.

Com o modo Garantia desligado, a captura fica mais precisa. Com ele ligado, ela fica mais agressiva e tenta salvar tambem pastas externas parecidas com o jogo.

### 6. Apps Ignorados

Alguns apps da Steam ficam rodando em segundo plano e nao representam uma sessao real de jogo. Um exemplo comum e o **Wallpaper Engine**.

Na aba **Configuracoes**, a lista **Apps ignorados** permite marcar esses AppIDs. Apps ignorados:

- nao iniciam sessao monitorada;
- nao criam backup automatico quando fecham;
- nao viram alvo automatico de captura;
- aparecem como ignorados quando detectados pela area de capturas.

---

## O Que E Salvo

AchievementBackup pode salvar:

- Saves da Steam em `userdata`.
- Saves em `remote`.
- `remotecache.vdf`.
- Estatisticas em `appcache/stats`.
- Arquivos de conquistas/cache.
- Arquivos Lua Tools e `stplug-in`.
- Configuracao global em `Steam/config/lua`.
- Saves externos por API/manifest em capturas.
- Saves externos por heuristica quando o Modo Garantia esta ligado.
- Depot cache em backups completos.
- Metadata com AppID, nome do jogo, contas, modo da captura e arquivos copiados.

---

## Interface

- **Backups:** lista backups completos, tamanho, data, contas e quantidade de arquivos.
- **Capturas:** lista jogos com capturas; ao abrir o jogo, lista as capturas daquele jogo.
- **Configuracoes:** modos de backup, hotkey, temas, apps ignorados, API/scanner de saves e portabilidade.
- **Mais informacoes:** mostra jogos identificados, AppIDs, contas e categorias salvas.

---

## Instalacao Manual

> Requisito: Steam com [Millennium](https://steambrew.app/) instalado.

1. Baixe ou clone este repositorio.
2. Copie a pasta interna `AchievementBackup/` para:

```text
Steam/
  plugins/
    AchievementBackup/
```

3. Reinicie a Steam.
4. Abra a Steam e procure o botao do AchievementBackup no canto inferior direito.

> Importante: evite manter uma copia antiga do `CalyRecall` ativa ao mesmo tempo, pois duas instancias podem tentar usar a mesma porta local `9999`.

---

## Estrutura Do Repositorio

```text
AchievementBackup/
  README.md
  license
  .gitignore
  AchievementBackup/
    backend/
      main.py
      server.py
      monitor.py
      achievement_backup.py
      hotkey.py
    frontend/
    public/
      index.js
    webkit/
    plugin.json
```

---

## Portabilidade

O painel permite exportar todos os backups para um `.zip` e importar novamente depois. Isso e util para formatar o PC, migrar de disco ou manter uma copia externa dos backups.

---

## Limitacoes Conhecidas

- A deteccao depende do comportamento da Steam, processos locais e AppIDs instalados.
- Alguns jogos usam caminhos de save muito especificos.
- Jogos com sincronizacao agressiva de nuvem podem exigir cuidado na restauracao.
- Duas versoes do plugin ativas podem causar conflito na porta local `9999`.
- A interface depende do carregamento correto do Millennium e das superficies da Steam.

---

## Creditos E Licenca

AchievementBackup foi desenvolvido a partir da base do **CalyRecall**, criado por **BruxinCore**.

Este repositorio mantem esse credito porque a arquitetura original, a ideia de monitoramento de sessao e parte da estrutura inicial vieram do CalyRecall. Antes de redistribuir publicamente qualquer versao derivada, verifique a licenca original e tenha permissao adequada do autor quando necessario.

---

## Aviso

Este projeto mexe com arquivos locais da Steam e de jogos. Use por sua conta e risco. Teste com jogos nao criticos antes de confiar em qualquer fluxo de restauracao.
