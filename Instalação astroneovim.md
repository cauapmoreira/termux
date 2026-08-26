# Instalação AstroNvim via terminal Termux

## Atualização do Termux

```batedor
atualização do pacote && atualização do pacote -y
```

Atualize uma lista de pacotes disponíveis e insta como versões mais recentes dos pacotes já instalados no Termux.

## Instalação dos pacotes

```batedor
pkg instalar git curl wget descompactar tar ripgrep fd fzf clang fazer cmake pkg-config -y
```

`git` — usado para controlar versões e trabalhar com repositórios do GitHub.

`inscrito` — usado para fazer requisições e baixar arquivos pela internet.

`wget` — usado para baixar arquivos pela internet.

`descompactar` — usado para extrair arquivos `.zíper`.

`alcatrão` — usado para criar e extrair arquivos compactados.

`ripgrep` — ferragem rápida para pesquisar texto dentro de arquivos e diretórios. O comando utilizado é `rg`.

`df` — ferragem para localizar arquivos e diretores rapidamente.

`fzf` — ferragem de busca e seleção interativa no terminal.

`barulho` — compilador usado para compilar programas escritos em C e C++.

`fazer` — ferramenta utilizada para automatizar processos de compilação.

`fazer cm` — ferrama usada para configurar projetos que exigem ser compilados.

`pkg-config` — ajuda programas a localizar bibliotecas instaladas e suas configurações.

`-y` — respondeu automaticamente `sim` às confirmações solicitadas pelo `pacote`.
## Instalação do Neovim

```bash
pkg install neovim -y
```

Instala o Neovim no Termux, que será utilizado como base para a instalação e configuração do AstroNvim.
