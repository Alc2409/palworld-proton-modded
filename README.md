# Palworld Proton Modded

Egg **exclusivo para Proton** em Pelican/Pterodactyl. Ele instala e executa o servidor dedicado **Windows do Palworld** dentro de um contêiner Linux usando Proton. Não é um egg para execução nativa Linux nem para Windows puro. O arquivo também prepara o `PalWorldSettings.ini`, instala o parser de configuração, habilita integração de console por `palcon`, instala `winmm.dll` para o ambiente modificado e pode instalar o PalDefender opcionalmente.

## O que esta versão faz

- Instala o App ID `2394010` do SteamCMD usando `+@sSteamCmdForcePlatformType windows`.
- Executa `PalServer-Win64-Shipping.exe` via Proton.
- Expõe as variáveis de configuração do mundo, jogadores, guildas, construção e PvP no painel.
- Cria `Engine.ini` com tick de rede em 60 e parâmetros de coleta de lixo ajustados. O objetivo é reduzir sobrecarga e instabilidade, não prometer uma taxa fixa de quadros nos clientes.
- Inicia o `palcon` em segundo plano para que a console do painel envie comandos ao servidor.
- Habilita `-enable-gamedata-api` e configura a API REST conforme as variáveis do painel.
- Inclui instalação opcional do PalDefender, mas ele deve ser tratado como incompatível com Linux/Proton até existir suporte confirmado.

## Arquivo

Use [`egg-palworld-proton-modded.json`](./egg-palworld-proton-modded.json) para importar no painel. O ambiente esperado é a imagem Proton `ghcr.io/parkervcp/steamcmd:proton`. Esta cópia pública usa o nome do autor e contém a senha inicial intermediária `PalAdm2026`; altere-a antes de colocar o servidor em produção.

## Ajustes mais importantes

A versão revisada remove a referência inexistente `{{ENABLE_GAMEDATA}}` do startup e usa diretamente a flag oficial `-enable-gamedata-api`. Também corrige a regra de `DEATH_PENALTY`, que tinha `ItemAndEquilment`/`ALL` e podia rejeitar opções válidas.

Antes de colocar o servidor na Internet, defina uma senha administrativa forte, limite o acesso às portas de administração, faça backup do mundo e teste o egg em uma instalação separada. O instalador baixa executáveis e DLLs de URLs externas; em produção, prefira fixar versões e validar hashes.

## Compatibilidade Proton-only

O egg depende de Proton, do binário Windows `PalServer-Win64-Shipping.exe`, de caminhos `Pal/Binaries/Win64` e da imagem `ghcr.io/parkervcp/steamcmd:proton`. Não use este arquivo como configuração para um servidor Linux nativo ou para um servidor Windows executado diretamente. O PalDefender permanece desativado por padrão porque o projeto informa suporte exclusivo a servidores Windows; em Proton ele deve ser considerado incompatível até teste específico.

## Compatibilidade e responsabilidade

Este é um egg comunitário, não uma distribuição oficial do Palworld, Proton, Pelican ou dos projetos de terceiros baixados pelo instalador. Atualizações do jogo, do Proton ou das ferramentas podem quebrar o startup. Leia [`ANALISE.md`](./ANALISE.md) antes de usar em produção.
