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

A extensão **Studio (ponte)**, que só avisa o aplicativo de computador que o SketchUp está aberto,
não aparece aqui: ela não tem atualizador. Atualizar a ponte é instalar o `.rbz` novo.

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
