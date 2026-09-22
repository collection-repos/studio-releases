# Studio — downloads

Os arquivos que o Studio baixa: os instaladores das extensões do SketchUp e os arquivos de versão
que elas consultam para se atualizar.

**Aqui não tem código.** O código vive em repositórios privados; este repositório existe para ser
público, e é a razão dele existir separado: a extensão procura a atualização de dentro do SketchUp
de quem a instalou, sem nenhuma senha para oferecer, e arquivo de repositório privado só baixa
autenticado. Um endereço privado funcionaria na máquina de quem publicou, logada no navegador, e
falharia calado na de todos os outros.

## O que tem em cada pasta

| Pasta                | Extensão                              | Quem consulta                                 |
| -------------------- | ------------------------------------- | --------------------------------------------- |
| `studio-plugin/`     | **Studio** — o seletor de cenas       | `latest.json` desta pasta, pela própria extensão |
| `studio-plugin/full/`| **Studio completo** — o Studio inteiro numa janela | `latest.json` desta pasta                |
| `studio-bridge/`     | **Studio (ponte)** — avisa o aplicativo que o SketchUp está aberto | `latest.json` desta pasta |

A ponte se atualiza sozinha desde a `0.3.0`, com um atualizador próprio e pequeno: ela é a
extensão que mais muda, porque cada capacidade nova que o aplicativo aprende a pedir sai numa
versão nova dela. Ela só instala pacote servido por **este** repositório, e é por isso que o
endereço acima não pode mudar de lugar sem mudar a extensão junto.

## Duas formas de publicar no mesmo repositório, e por quê

| O que se atualiza | Onde os arquivos ficam | Por quê |
| ----------------- | ---------------------- | ------- |
| As extensões do SketchUp | Na **árvore**, nas pastas acima, lidas por `raw.githubusercontent.com` | O atualizador embarcado exige resposta 200 direta, e endereço de release responde com redirecionamento |
| O aplicativo de computador | Nas **releases** do repositório, uma tag por versão, com o instalador e o `latest.yml` como anexos | É o formato que o `electron-updater` sabe ler, e ele segue redirecionamento sozinho |

As duas convivem sem se atropelar: uma mexe em arquivos, a outra em releases e tags. O aplicativo
não usa a árvore; suas tags são `v<versão>` e os anexos de cada release são
`Studio-Setup-<versão>.exe`, `latest.yml` e o `.blockmap` correspondente.

### Regra: nenhuma release comum além das do aplicativo

O `electron-updater`, na configuração que o aplicativo usa, **não procura a release dele**: ele
pergunta ao GitHub qual é a release `latest` do repositório inteiro e só então procura o
`latest.yml` entre os anexos dela. Uma release comum publicada aqui por qualquer outro motivo — do
plugin, por exemplo — passa a ser a `latest` do repositório, o aplicativo procura o `latest.yml`
nela, não acha, e **desiste sem mostrar erro nenhum**: ninguém recebe atualização e nada aparece na
tela para explicar.

Por isso o plugin vive na árvore, e não em releases. Se algum dia for preciso publicar uma release
aqui que não seja do aplicativo, ela tem que ser marcada como **pre-release**, que é o que o
endpoint de `latest` ignora.

## Como um `latest.json` é lido

```json
{
  "version": "0.9.1",
  "url": "https://raw.githubusercontent.com/collection-repos/studio-releases/main/studio-plugin/studio-0.9.1.rbz",
  "sha256": "…",
  "min_sketchup": 21,
  "changelog": "…",
  "published_at": "2026-09-22"
}
```

A extensão instalada compara `version` com a sua e, se a de cá for maior, baixa o `url`, confere o
`sha256` e instala. Um `sha256` que não corresponde ao arquivo faz toda atualização ser recusada
como "download corrompido" — por isso o manifesto é **gerado** pelo empacotador junto do pacote, e
nunca escrito à mão.

Os endereços são de `raw.githubusercontent.com` de propósito: ele responde direto, e o endereço de
release responde com um redirecionamento que o transporte do SketchUp pode não seguir.

## Os pacotes são assinados

Cada `.rbz` publicado aqui passou pela assinatura da Trimble (uma por extensão). Sem assinatura, o
SketchUp só instala com a política de extensões em "Irrestrito", que não é o que se pede a quem
está instalando um plugin.
