# Análise técnica e recomendações

## Veredito

O egg é uma base funcional para hospedar o servidor dedicado do Palworld em Linux quando o objetivo é usar recursos do ecossistema Windows/modding por Proton. Ele reúne muitas variáveis úteis e já automatiza boa parte da instalação. Porém, a cópia original não deveria ser publicada sem tratamento: traz uma senha administrativa padrão (`ChangeMe123`), um e-mail no campo `author`, downloads flutuantes de `releases/latest` e uma opção de anti-cheat que o próprio projeto informa ser exclusiva para servidores Windows.

A versão pública deste repositório é uma cópia revisada. O arquivo original não foi republicado.

## Pontos positivos

1. **Instalação automatizada.** Usa SteamCMD com o App ID correto do servidor dedicado do Palworld (`2394010`) e força a plataforma Windows antes de instalar os arquivos. Isso combina com a execução do binário Windows pelo Proton.
2. **Boa cobertura de configuração.** Há 88 variáveis para mundo, Pals, jogador, guilda, construção, PvP e servidor. Isso é mais prático que editar manualmente o `PalWorldSettings.ini`.
3. **Integração com o painel.** O parser é executado no startup e o `palcon` lê comandos de `/dev/stdin`, permitindo que a console do painel envie comandos ao processo.
4. **Parâmetros de desempenho explícitos.** O egg inclui `-useperfthreads`, `-NoAsyncLoadingThread` e `-UseMultithreadForDS`, além de um `Engine.ini` com tick de rede em 60. Esses parâmetros existem na documentação oficial, mas a documentação atual observa que, no Palworld 1.0 ou posterior, deixar esses três flags de fora pode melhorar o desempenho; portanto não se deve prometer “60 FPS” apenas por causa deles.
5. **Backup de configuração.** Se `PalWorldSettings.ini` já existe, o instalador cria uma cópia datada antes de substituir o arquivo pelo template padrão.

## Pontos negativos e riscos

### Segurança

- `ADMIN_PASSWORD` vinha com `ChangeMe123`. Isso é uma credencial conhecida e não pode ser usada em produção. A versão pública deixa o valor vazio para obrigar a configuração no painel.
- O instalador baixa executáveis e DLLs por `curl` sem checksum e, em alguns casos, pela tag `latest`. Uma atualização ou comprometimento da origem altera o que será executado sem revisão do egg.
- `palcon` recebe comandos vindos da entrada do painel. O acesso ao painel e às portas administrativas precisa ser restrito e autenticado.
- O arquivo original expõe `exemplo@gmail.com` como autor. A cópia pública usa um marcador neutro.

### Compatibilidade

- O PalDefender é baixado apenas quando `INSTALL_PALDEFENDER=True`, mas o repositório do projeto informa que ele é beta, fechado e disponível exclusivamente para servidores dedicados Windows. Em Proton/Linux, essa opção deve permanecer `False` até haver suporte confirmado.
- O servidor usa caminhos Windows (`Pal/Binaries/Win64`) e depende do comportamento específico da imagem `ghcr.io/parkervcp/steamcmd:proton`. Isso torna o egg sensível a mudanças na imagem e no Proton.
- `winmm.dll`, RE-UE4SS, parser e `palcon` são componentes de terceiros. A compatibilidade precisa ser revalidada após cada atualização do Palworld.

### Correção de configuração

- O startup original terminava com `-rcon {{ENABLE_GAMEDATA}}`, mas não há variável `ENABLE_GAMEDATA` no JSON. Ao mesmo tempo, existe `EXTRA_FLAGS` com `-enable-gamedata-api`, porém essa variável não é interpolada no comando. A versão pública usa diretamente `-enable-gamedata-api`.
- A regra original de `DEATH_PENALTY` exigia simultaneamente `ItemAndEquipment` e `ItemAndEquilment`, além de misturar `All` e `ALL`. Isso pode fazer o painel rejeitar opções válidas. A versão pública mantém somente `None,Item,ItemAndEquipment,All`.
- Os nomes “60FPS” e “60 FPS” descrevem principalmente tick de rede e parâmetros do servidor; não garantem 60 quadros por segundo para clientes.
- `MAX_PLAYERS` é travado no painel como não editável em 32, embora a regra aceite até 100. Isso é uma decisão razoável para estabilidade, mas a descrição deveria deixar claro que é uma política do egg, não um limite universal do Palworld.
- `AUTO_SAVE_SPAN` está em 30 minutos. Para um servidor modificado, considerar um intervalo menor, como 5–15 minutos, reduz perda potencial após crash, mas aumenta I/O.

## O que eu ajustaria por cenário

### Servidor cooperativo estável

Mantenha PvP, friendly fire, hardcore, morte permanente e randomizadores desligados. Use `MAX_PLAYERS=16–32`, `BASE_CAMP_WORKER_MAX_NUM=10–15`, `PAL_SPAWN_NUM_RATE=1.0`, `DROP_ITEM_MAX_NUM` entre 1000 e 2000 e `INSTALL_PALDEFENDER=False`. Defina senha administrativa forte e uma senha de entrada se o servidor não for realmente público.

### Servidor casual com progressão mais rápida

Aumente moderadamente `EXP_RATE` para 1.5–2.0, `PAL_CAPTURE_RATE` para 1.5–2.0, `WORK_SPEED_RATE` para 1.25–1.5 e reduza `PAL_EGG_DEFAULT_HATCHING_TIME` de 72 horas para algo compatível com o grupo. Evite aumentar muito spawn, workers, drops e velocidade ao mesmo tempo, porque isso torna a progressão trivial e pode elevar CPU, memória e quantidade de entidades.

### Servidor PvP

Só ative `ENABLE_PLAYER_TO_PLAYER_DAMAGE` com regras explícitas, backup frequente e teste de todas as opções de drop. Friendly fire deve continuar desligado salvo se for intencional. Não deixe RCON/API expostos diretamente à Internet; restrinja por firewall ou rede privada.

## Ordem recomendada de melhorias

1. Definir senha administrativa única e forte no painel.
2. Fixar versões e hashes de parser, Proton, RE-UE4SS, `palcon`, `winmm.dll` e PalDefender, em vez de usar `latest`.
3. Corrigir e testar startup, especialmente API, RCON, console e parada limpa.
4. Remover ou bloquear a instalação do PalDefender quando o ambiente for Linux/Proton.
5. Criar backup automático do diretório `Pal/Saved` e testar restauração.
6. Medir CPU, memória, tick rate e tamanho dos saves com a população real antes de aumentar players, Pals ou workers.

## Fontes

[1]: https://docs.palworldgame.com/getting-started/deploy-dedicated-server/ "Palworld — Deploy dedicated server"
[2]: https://docs.palworldgame.com/settings-and-operation/arguments/ "Palworld — Configure the server"
[3]: https://github.com/pelican-eggs/games-steamcmd/blob/main/palworld/egg-palworld-proton.yaml "Pelican Eggs — Palworld Proton egg"
[4]: https://github.com/Ultimeit/PalDefender "PalDefender — repository and compatibility notice"
[5]: https://github.com/UE4SS-RE/RE-UE4SS/releases "RE-UE4SS — releases and installation notes"
