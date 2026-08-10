# Tutorial: Transforme seu Celular em um Servidor NAS Caseiro
#### Dificuldade: Fácil

Transformar um celular velho, antigo ou sem uso em um servidor é uma forma de reutilizar o aparelho, evitando o descarte eletrônico e dando uma segunda vida ao equipamento.

Vamos montar um **servidor NAS** (Network Attached Storage), que é um servidor projetado para armazenar e fornecer acesso a arquivos pela rede local. Com ele, você consegue guardar e acessar seus arquivos de qualquer dispositivo conectado ao mesmo Wi-Fi, direto pelo navegador.

Então bora começar esse servidorzinho, que ele é muito top!

---

## 📺 Vídeo

Prefere assistir ao invés de ler? Tem um vídeo completo cobrindo esse tutorial:

[![Thumbnail do vídeo - Servidor NAS no Celular](https://img.youtube.com/vi/jMXeb5JSE1s/maxresdefault.jpg)](https://www.youtube.com/watch?v=jMXeb5JSE1s)

---

## O que você vai precisar

- Um celular Android (qualquer versão razoavelmente recente)
- Acesso à internet para baixar os apps e pacotes
- Rede Wi-Fi local (todos os dispositivos devem estar na mesma rede para acessar o servidor)

---

## 1) Baixar a loja F-Droid

O F-Droid é uma loja gratuita com vários apps open-source. A maior vantagem é poder se desvincular da Google Play Store e baixar apps diretamente, sem precisar de login ou qualquer tipo de rastreamento.

**Link do F-Droid:** https://f-droid.org/

Após entrar no site, baixe e instale o `.apk` do F-Droid normalmente. O Android pode pedir permissão para instalar apps de fontes desconhecidas, só liberar.

---

## 2) Baixar o Termux

Após instalar o F-Droid, vamos instalar o **Termux**. Ele é um emulador de terminal Linux que roda diretamente no Android, sem precisar de root. É através dele que vamos instalar e gerenciar todos os serviços do nosso servidor.

> **Atenção:** Baixe o Termux pelo F-Droid, **não pela Google Play Store**. A versão da Play Store está desatualizada e pode causar problemas.

Pode aparecer um aviso do **Google Play Protect** tentando bloquear o download. Clique em *Mais detalhes* e instale mesmo assim, é seguro.

---

## 2.1) Atualizar o Termux

Após instalar e abrir o Termux pela primeira vez, a primeira coisa a fazer é atualizar o sistema de pacotes:

```bash
pkg update && pkg upgrade -y
```

Isso garante que você está com as versões mais recentes de tudo. Pode demorar um pouco dependendo da conexão.

---

## 3) Liberar acesso ao armazenamento

Para que o Termux consiga acessar os arquivos do celular, precisamos dar permissão de armazenamento:

```bash
termux-setup-storage
```

O Android vai exibir um pop-up pedindo permissão. Clique em **Permitir**. Sem isso, o File Browser não consegue enxergar seus arquivos.

---

## 4) Instalar o OpenSSH

Agora vamos instalar o **OpenSSH**, que nos permite acessar o celular de outros dispositivos via terminal (SSH). Isso é útil para gerenciar o servidor sem precisar pegar o celular fisicamente.

```bash
pkg install openssh -y
```

### 4.1) Iniciar o SSH

Para iniciar o servidor SSH:

```bash
sshd
```

### 4.2) Deixar o SSH iniciar automaticamente

Para que o SSH inicie sempre que o celular ligar, adicione ao `.bashrc`:

```bash
echo "sshd" >> ~/.bashrc
```

---

## 5) Instalar o File Browser

O **File Browser** vai ser a nossa interface NAS. Ele é leve, open-source e tem uma interface web simples e funcional que permite:

- Enviar (upload) arquivos do computador ou celular para o servidor
- Baixar arquivos do servidor
- Ler arquivos diretamente no navegador (PDF, imagens, vídeos, docs)
- Criar pastas e organizar tudo

Para instalar, execute:

```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```

Esse comando baixa e instala o File Browser direto do repositório oficial.

---

## 6) Iniciar o File Browser

Para iniciar o File Browser:

```bash
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared
```

O que cada parte significa:
- `-a 0.0.0.0` — aceita conexões de qualquer dispositivo na rede
- `-p 8080` — define a porta de acesso (você pode trocar se quiser)
- `-r ~/storage/shared` — define a pasta raiz que o File Browser vai exibir (toda a memória interna do celular)

---

## 6.1) Impedir que o Termux "adormeça"

Por padrão, o Android pode matar processos em segundo plano para economizar bateria. Para evitar que o File Browser seja encerrado, rode:

```bash
termux-wake-lock
```

Esse comando faz três coisas importantes:
1. Impede que o Android mate o processo do File Browser
2. Mantém a conexão Wi-Fi ativa mesmo com a tela desligada
3. Garante respostas rápidas para quem acessa o servidor

---

## 7) Deixar o File Browser iniciar automaticamente

Da forma que configuramos no passo 6, o servidor para quando o celular reinicia. Para torná-lo totalmente automático, vamos usar o **Termux:Boot**.

### 7.1) Instalar o Termux:Boot

Instale o **Termux:Boot** pelo F-Droid. Após instalar, **abra o app pelo menos uma vez** — isso é necessário para que o sistema registre o serviço de boot.

### 7.2) Criar a pasta de boot

```bash
mkdir -p ~/.termux/boot
```

### 7.3) Criar o script de inicialização

```bash
vim ~/.termux/boot/start-services.sh
```

Dentro do vim, pressione `a` para entrar no modo de edição e escreva:

```bash
#!/data/data/com.termux/files/usr/bin/sh
termux-wake-lock
sshd
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared > /dev/null 2>&1 &
```

Para salvar e sair do vim: pressione `Esc`, depois digite `:wq` e pressione Enter.

### 7.4) Tornar o script executável

```bash
chmod +x ~/.termux/boot/start-services.sh
```

Pronto! A partir de agora, toda vez que o celular ligar, o SSH e o File Browser vão iniciar automaticamente.

---

## 8) Trocar a senha padrão do File Browser

O File Browser vem com o usuário `admin` e uma senha padrão. **Troque imediatamente** por uma senha sua:

```bash
filebrowser users update admin --password SUA_SENHA_AQUI
```

Substitua `SUA_SENHA_AQUI` pela senha que quiser usar.

---

## 9) Acessar o Servidor NAS

Com o File Browser rodando, precisamos descobrir o IP do celular na rede:

```bash
ifconfig
```

Procure a seção `wlan0` no resultado. O IP vai aparecer ao lado de `inet`, algo como `192.168.1.100`.

Com o IP em mãos, acesse pelo navegador de qualquer dispositivo na mesma rede:

```
http://192.168.1.100:8080
```

Faça login com `admin` e a senha que você definiu no passo 8.

> **Dica:** Cada dispositivo na rede pode ter um IP diferente. Se o IP do seu celular mudar, repita o `ifconfig` para descobrir o novo.

---

## Passo extra: Monitorar CPU e RAM via SSH

Para acompanhar o desempenho do servidor pelo terminal (via SSH de outro dispositivo), instale o `htop`:

```bash
pkg install htop -y
```

E execute com:

```bash
htop
```

---

## Considerações finais

Nosso Servidor NAS caseiro está pronto! Algumas observações importantes:

- **Performance de upload:** Para velocidades melhores, um **adaptador de rede USB** (USB-C para Ethernet) faz diferença considerável em relação ao Wi-Fi.
- **Velocidade da rede:** Se o celular estiver longe do roteador, o upload pode ficar lento. Tente manter o servidor com boa cobertura de sinal.
- **Limitações do hardware:** A performance varia de celular para celular. Celulares mais antigos ou com pouca RAM podem ficar lentos com muitos acessos simultâneos.
- **Expandindo o servidor:** Como cada serviço roda em uma porta diferente, é possível hospedar outros serviços no mesmo celular (servidor de música, Minecraft, etc.) sem conflito.

---

É isso rapaziada, curtiu?
Apoia o canal se esse tutorial foi útil, considera virar membro.

Caso algo tenha ficado confuso ou você tenha alguma melhoria, me avisa!

Tamo junto ✌️

🔧 Minhas modificações no servidor

«Esta seção documenta as alterações que fiz depois de seguir o tutorial original.
O objetivo é registrar a configuração específica do meu servidor para facilitar uma futura reinstalação ou recuperação.»

10) Comandos personalizados para administrar o servidor

Além da configuração original, foram criados alguns comandos para facilitar o gerenciamento do servidor pelo Termux.

Os comandos personalizados são:

Comando| Função
"nas-on"| Inicia os serviços do servidor
"nas-off"| Desliga somente o File Browser
"sd"| Abre o File Browser usando a raiz do cartão SD
"raiz"| Volta o File Browser para o armazenamento interno
"login"| Conecta ao servidor por SSH a partir do celular cliente

10.1) "nas-on"

Foi criado um comando para iniciar os serviços através do script do Termux:Boot:

#!/data/data/com.termux/files/usr/bin/bash

bash ~/.termux/boot/start-services.sh

O comando pode ser executado simplesmente com:

nas-on

10.2) "nas-off"

O comando original foi modificado para não desligar o SSH. Dessa forma, o servidor pode continuar sendo administrado remotamente mesmo quando o File Browser estiver desligado.

#!/data/data/com.termux/files/usr/bin/bash

pkill filebrowser
termux-wake-unlock

Para desligar o File Browser:

nas-off

O "sshd" continua funcionando.

10.3) Acessar o cartão SD pelo File Browser

Foi criado o comando "sd" para iniciar o File Browser diretamente na raiz do cartão SD.

O cartão SD utilizado pelo servidor está montado em:

/storage/00B3-03E9

O script utilizado é:

#!/data/data/com.termux/files/usr/bin/bash

pkill filebrowser 2>/dev/null
filebrowser -a 0.0.0.0 -p 8080 -r /storage/00B3-03E9 -d ~/filebrowser.db > ~/.filebrowser.log 2>&1 &

Depois disso, basta executar:

sd

O File Browser passa a mostrar a raiz do cartão SD.

10.4) Voltar para o armazenamento interno

Para voltar ao funcionamento normal, foi criado o comando "raiz".

#!/data/data/com.termux/files/usr/bin/bash

pkill filebrowser 2>/dev/null
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared -d ~/filebrowser.db > ~/.filebrowser.log 2>&1 &

Executando:

raiz

o File Browser volta a utilizar:

~/storage/shared

como sua raiz.

Os scripts "sd" e "raiz" executam o File Browser em segundo plano, permitindo continuar utilizando o Termux normalmente.

10.5) Acesso SSH pelo celular cliente

Para facilitar o acesso remoto ao Termux do servidor, foi criado um comando "login" no celular utilizado como cliente.

O SSH do servidor utiliza a porta "8022".

O script "login" é:

#!/data/data/com.termux/files/usr/bin/bash

ssh -p 8022 'USUARIO_DO_SERVIDOR'@'IP_DO_SERVIDOR'

Depois de dar permissão de execução:

chmod +x ~/login

o acesso pode ser feito simplesmente com:

login

«Importante: não guardar senhas, chaves privadas ou outras credenciais diretamente neste arquivo se ele for publicado ou sincronizado com um repositório público.»

10.6) Prioridade do comando "login"

O Termux já possui um programa chamado "login". Para que o comando personalizado seja encontrado primeiro, o diretório HOME foi colocado no início do "PATH".

No ".bashrc" do celular cliente:

export PATH="$HOME:$PATH"

Depois:

source ~/.bashrc

É possível verificar qual "login" será executado com:

type -a login

O resultado deve mostrar primeiro o script localizado em:

~/login

10.7) Fluxo de utilização

Com essas modificações, o servidor pode ser administrado desta forma:

login
   ↓
acesso SSH ao servidor
   ↓
nas-on
   ↓
SSH + File Browser funcionando

Para utilizar o armazenamento do cartão SD:

sd
   ↓
File Browser → cartão SD

Para voltar ao armazenamento interno:

raiz
   ↓
File Browser → armazenamento interno

Para desligar somente a interface web:

nas-off
   ↓
File Browser desligado
   ↓
SSH continua funcionando

Dessa forma, o SSH permanece disponível como canal de administração, enquanto o File Browser pode ser ligado, desligado e alternado entre o armazenamento interno e o cartão SD sem precisar reiniciar o servidor.
