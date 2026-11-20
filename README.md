# Palworld Proton Modded

Egg para Pelican/Pterodactyl focado exclusivamente em executar o servidor dedicado **Windows do Palworld** em Linux usando **Proton**.

## Arquivo

Importe [`egg-palworld-proton-modded.json`](./egg-palworld-proton-modded.json) no painel.

## O que ele faz

- Instala o servidor dedicado do Palworld pelo SteamCMD usando o App ID `2394010`.
- Executa `PalServer-Win64-Shipping.exe` por meio do Proton.
- Configura o servidor através das variáveis disponíveis no painel.
- Prepara ajustes de rede e garbage collector no `Engine.ini`.
- Integra o `palcon` para comandos pela console do painel.
- Habilita a Game Data API.
- Instala componentes de modding usados pelo ambiente Proton.

## Compatibilidade

Este egg depende da imagem `ghcr.io/parkervcp/steamcmd:proton` e foi criado para Proton. Não é destinado a servidor Linux nativo nem a servidor Windows executado diretamente sem Proton.

O PalDefender permanece desativado por padrão, pois o projeto informa suporte para servidores Windows. Em Proton, ele deve ser considerado incompatível até ser validado em testes específicos.

## Segurança

Defina uma senha administrativa própria no painel antes de colocar o servidor em produção. A senha inicial presente no arquivo é apenas uma senha intermediária de instalação e fica visível publicamente.

Autor: `Alc2409`
