# Prompt para IA — seletor local de skins e fogos

**Repositório do meu projeto:** COLE AQUI O LINK DO GITHUB  
**Branch/commit a analisar:** branch padrão (ou indique aqui)

Analise o repositório acima antes de editar. Trabalhe no código existente, preserve a arquitetura e o estilo do projeto, e faça a menor alteração integrada possível.

## Acesso aos assets — importante

Não estou anexando o ZIP de assets nesta conversa. Portanto:

1. Primeiro verifique se o repositório já contém o catálogo/imagens das skins ou uma pasta de assets equivalente. Se estiverem lá, use esses arquivos.
2. Se não estiverem, você pode usar os endereços públicos oficiais abaixo para buscar os metadados/imagens, se seu ambiente tiver acesso à internet e puder adicioná-los ao projeto. Use caminhos locais no app depois de baixá-los; não dependa de CDN/rede externa em runtime.
3. Se o repositório for privado/inacessível, ou se não conseguir obter os assets, **não finja que os analisou, não crie imagens falsas e não implemente uma lista incompleta como se fosse completa**. Diga exatamente o que está faltando e peça acesso ao repo ou que eu adicione os assets ao repositório.

Fontes públicas de referência:
- Traduções/lista de itens: `https://s3.mope.io/mope-prod/translations/items/en.json`
- Catálogo público da loja: `https://api.mope.io/store`
- Miniatura de skin de animal: `https://s3.mope.io/mope-prod/items/{item_id}/{animal_id}.ui.webp`
- Miniatura normal de animal: `https://mope.io/assets/animals/{biome}/{animal_id}/{animal_id}.ui.webp`
- Fogo padrão: `https://mope.io/assets/abilities/fireball/0/{dynamic|static}/{0..4}.webp`
- Fogo especial: `https://s3.mope.io/mope-prod/items/abilities/fireball/{asset_key}/{dynamic|static}/{0..4}.webp`
- Os tiers e os efeitos do cliente devem ser conferidos no registro atual do jogo/repositório; a referência consultada tinha 17 tiers, 103 animais, 478 variantes de skin animal e 25 variantes Fireball. A lista da loja individual era menor, então não use apenas a loja para decidir quais skins existem.

## Objetivo

Na tab existente **Skins**, substitua o seletor/lista antiga por duas opções internas: **Skins** e **Fogos**. A experiência final deve permitir escolher skins e efeitos separadamente para cada espécie, afetando somente a aparência do meu jogador local.

## 1. Limpar o seletor antigo antes de adicionar as opções

Antes de implementar as duas opções novas, inspecione a tab **Skins** atual. Remova/substitua a lista, grade, botões e lógica antigos que exibem/selecionam skins dentro dela, para não deixar a interface antiga duplicada junto da nova.

Mantenha a tab externa **Skins**, a navegação e qualquer funcionalidade/configuração não relacionada ao seletor antigo. Reutilize componentes úteis, mas não mantenha duas listas concorrentes. Não apague assets úteis do projeto.

## 2. Opção interna “Skins”

1. Mostre os tiers na ordem do jogo, do Tier 1 (rato/mouse) ao Tier 17 (Black Dragon e King Dragon), com todos os tiers e animais do registro disponível. Não ordene alfabeticamente.
2. Mostre a imagem normal/padrão de cada animal. Ao abrir um animal — por exemplo, Mouse — mostre a opção normal e todas as variantes associadas **àquele animal específico**; não misture skins de outras espécies.
3. **Preview obrigatório:** cada opção deve ter uma miniatura real da skin, não apenas um nome. Mostre também um preview maior da opção focada/selecionada. Ao clicar/tocar numa miniatura, atualize o preview; o fluxo deve funcionar em mobile sem depender de hover. Se possível, mostre também a prévia aplicada ao meu personagem local antes de confirmar.
4. Salve uma escolha independente para cada animal. Exemplo: a escolha de `mouse` só aparece quando eu estiver como mouse; a escolha de `wolf` só quando eu estiver como wolf. Ao mudar de espécie, use a preferência daquela espécie. Ofereça “Padrão/Normal” para restaurar o visual normal daquele animal.
5. Destaque qual opção está selecionada. Use o padrão atual da interface para confirmar/aplicar; se não houver confirmação separada, selecionar a miniatura pode aplicar a escolha localmente, mas o preview deve mudar imediatamente.
6. **Somente meu personagem:** aplique a skin por entidade e verifique que a entidade é o jogador local. Não altere o visual de outros jogadores, inclusive os da mesma espécie; não sobrescreva globalmente a textura de uma espécie.

## 3. Opção interna “Fogos”

1. Mostre os animais que usam o efeito Fireball: Dragon, Phoenix/Fênix, Black Dragon (BD) e King Dragon (KD). Use as variantes compatíveis de cada grupo.
2. Permita escolher uma variante de fogo separada para cada animal, incluindo “Original do jogo”. A escolha da Fênix, por exemplo, não deve alterar as escolhas do Dragon, BD ou KD.
3. Para buscar/gerar os efeitos, considere estas chaves identificadas no cliente atual:
   - Padrão: `default`
   - Dragon: `dragon_fiery`, `dragon_gold`, `dragon_mythical_serpent`, `dragon_purple_haze`, `dragon_astral`, `dragon_rose`
   - Fênix: `phoenix_alpha`, `phoenix_aqua`, `phoenix_ash`, `phoenix_ice`, `phoenix_icicle`, `phoenix_red_giant`, `phoenix_sundown_flames`
   - BD: `black_dragon_aurora`, `black_dragon_azure`, `unreleased_bd`, `black_dragon_streamline`
   - KD: `birthday_mistik`, `king_dragon`, `king_dragon_king_mistik`, `king_dragon_king_nightbringer`, `king_dragon_king_stan`, `king_dragon_queen_celeste`, `king_dragon_queen_scarlet`
4. Cada variante é uma sequência, não uma opção por frame: `dynamic/0..4` é o projétil em movimento e `static/0..4` é a animação parada/impacto. **Preview obrigatório:** apresente a sequência dinâmica animada (e, se possível, a estática) antes da seleção.
5. Aplique o override somente ao fogo lançado pelo meu jogador local e somente para a espécie correspondente. Os projéteis dos outros jogadores devem continuar iguais. Se o código não identificar com segurança quem lançou o projétil, não aplique uma mudança global: explique a limitação e peça orientação.
6. A preferência de fogo é independente da skin do animal e deve continuar salva quando eu trocar de skin ou de espécie.

## Persistência e limites técnicos

- Persista localmente, por exemplo em `localStorage` com uma chave versionada (`mope.cosmetics.v1`) e estrutura equivalente a `skinByAnimal` e `fireByAnimal`.
- Não envie escolhas cosméticas ao servidor. Não altere protocolo, mensagens de rede, stats, hitboxes, cooldowns, dano, habilidades ou regras de jogo. É uma personalização visual local.
- Não altere entidades remotas nem substitua texturas globais compartilhadas.
- Baixe/use os arquivos localmente no projeto quando possível; não use links `sandbox:` e não dependa de internet/CDN em runtime.
- Não filtre skins antigas/limitadas só por não aparecerem na loja atual; verifique a lista pública de traduções e os assets. Diferencie “asset encontrado” de “skin comprável atualmente”.
- Faça a UI funcionar em desktop e mobile e siga os padrões existentes no repo.

## Processo e entrega

1. Informe quais arquivos do repo contêm a tab antiga, o estado do jogador local e o carregamento de texturas/projéteis.
2. Remova/substitua o seletor antigo dentro da tab, conforme descrito, e implemente Skins/Fogos.
3. Se os assets não estiverem acessíveis, pare e diga o que preciso adicionar ao repo; não simule que os baixou.
4. Ao concluir, liste os arquivos alterados, como executar/testar e quaisquer assets que faltaram.

## Critérios de aceitação

- A tab externa **Skins** continua existindo, mas não mostra a lista antiga duplicada; contém as opções internas **Skins** e **Fogos**.
- Todos os tiers/animais aparecem na ordem correta. Cada opção de skin mostra imagem real, preview maior e alternativa normal.
- Uma escolha para Mouse afeta só meu Mouse local; outros jogadores não mudam. As escolhas de outras espécies reaparecem quando eu mudar para elas e persistem após recarregar.
- Posso configurar e pré-visualizar fogos diferentes para Dragon, Fênix, BD e KD, independentemente da skin escolhida.
- Os fogos dos outros jogadores não mudam. Nenhuma escolha cosmética altera gameplay ou é enviada ao servidor.
