# Catálogo de comandos do dekH

Este documento lista os comandos existentes no desenvolvimento atual do
dekH 0.4.2. Há quatro grupos diferentes:

1. comandos normais presentes no sistema-base;
2. comandos internos do shell;
3. comandos do console de recuperação e executáveis de diagnóstico;
4. comandos FreeBSD presentes somente nas imagens experimentais de teste.

Os comandos `bsd*` e `dyn*` ainda não estão instalados na VM principal da
release. Eles são adicionados à fixture dekHFS v4 criada por
`scripts/test-freebsd-libc.sh`.

## Shell e controle da sessão

| Comando | Uso principal | Explicação |
|---|---|---|
| `sh [ARQUIVO]` | Shell interativo ou executor de script | Interpreta comandos, scripts linha a linha, aspas, escapes, variáveis, pipelines e redirecionamentos. |
| `help` | `help` | Mostra o resumo dos comandos e recursos do shell. |
| `man COMANDO` | `man ls` | Exibe uma página curta para os comandos documentados. `man -k` lista as páginas disponíveis. |
| `cd [CAMINHO]` | `cd /tmp` | Altera o diretório atual. Sem argumento usa `$HOME`. |
| `export NOME=VALOR` | `export PATH=/bin` | Cria ou altera uma variável de ambiente. |
| `unset NOME` | `unset TEMP` | Remove uma variável de ambiente. |
| `status` | `status` | Mostra o código de saída do último comando. |
| `exit [CÓDIGO]` | `exit 0` | Encerra o shell. No shell de login, encerra a sessão. |
| `logout [CÓDIGO]` | `logout` | Equivalente a `exit`. |
| `jobs` | `jobs` | Lista os trabalhos iniciados em segundo plano pelo shell. |
| `fg [PID]` | `fg` | Traz o trabalho indicado, ou o mais recente, para primeiro plano. |
| `wait [PID]` | `wait` | Aguarda um trabalho específico ou todos os trabalhos pendentes. |
| `rescue` | `rescue` | Solicita o console de recuperação autenticado como root. |
| `NOME=VALOR` | `TEMP=/tmp` | Atribui uma variável diretamente no shell. |
| `COMANDO &` | `sleep 5 &` | Executa o comando em segundo plano. |

O shell aceita pipelines de até três estágios e os redirecionamentos `<`,
`>`, `>>`, `2>`, `2>&1`. A expansão disponível inclui `$NOME`, `${NOME}` e
`$?`.

## Arquivos e diretórios

| Comando | Uso principal | Explicação |
|---|---|---|
| `echo [TEXTO...]` | `echo olá` | Imprime os argumentos, separados por espaços, e uma quebra de linha. |
| `pwd` | `pwd` | Mostra o diretório atual. |
| `ls [-l] [CAMINHO]` | `ls -l /etc` | Lista um diretório ou uma entrada. `-l` mostra tipo, modo, proprietário e tamanho. |
| `cat [ARQUIVO...]` | `cat /etc/hostname` | Copia arquivos para a saída; sem arquivo, ou com `-`, lê a entrada padrão. |
| `cp [-r] ORIGEM DESTINO` | `cp a b` | Copia um arquivo com substituição atômica. `-r` realiza cópia transacional de uma árvore. |
| `mv ORIGEM DESTINO` | `mv antigo novo` | Renomeia ou move uma entrada dentro do dekHFS. |
| `mkdir DIRETÓRIO` | `mkdir dados` | Cria um diretório. |
| `rmdir DIRETÓRIO` | `rmdir dados` | Remove um diretório vazio. |
| `rm ARQUIVO` | `rm temporário` | Remove um arquivo regular. Não remove diretórios. |
| `touch ARQUIVO` | `touch novo` | Cria um arquivo vazio se ele ainda não existir. |
| `chmod MODO CAMINHO` | `chmod 600 segredo` | Define permissões usando um modo octal. |
| `env [NOME]` | `env PATH` | Sem argumento lista o ambiente; com um nome mostra seu valor. |
| `printenv [NOME]` | `printenv HOME` | Alias funcional de `env`. |
| `edit ARQUIVO` | `edit notas.txt` | Abre o editor de texto linha a linha do dekH. |

No editor, `a` acrescenta texto, `i N` insere antes da linha, `d N` remove a
linha, `p` imprime, `w` salva, `q` sai se não houver alterações e `q!`
descarta as alterações. Uma linha contendo apenas `.` termina uma inserção.

## Processos

| Comando | Uso principal | Explicação |
|---|---|---|
| `ps` | `ps` | Lista PID, UID, estado e nome dos processos ativos. |
| `sleep SEGUNDOS` | `sleep 2` | Suspende a tarefa por 0 a 86400 segundos sem consumir o orçamento normal de CPU. |
| `kill PID` | `kill 12` | Solicita o encerramento do processo indicado, respeitando propriedade e privilégios. |

O sistema limita cada processo a três segundos de CPU e mantém até oito
contextos de tarefa. Espera por entrada, pipe ou timer não consome esse limite
como execução ativa.

## Utilitários de texto

Todos aceitam no máximo 65536 bytes de entrada; `-` representa a entrada
padrão. A ordenação e as contagens operam sobre bytes UTF-8, sem uma biblioteca
completa de locale ou expressões regulares.

| Comando | Uso principal | Explicação |
|---|---|---|
| `grep [-n] [-i] [--] TEXTO [ARQUIVO]` | `grep -n root /etc/passwd` | Imprime linhas que contêm literalmente o texto. `-i` ignora caixa ASCII. |
| `head [-n N] [ARQUIVO]` | `head -n 5 notas` | Imprime as primeiras linhas; o padrão é 10. |
| `tail [-n N] [ARQUIVO]` | `tail -n 5 notas` | Imprime as últimas linhas; o padrão é 10. |
| `wc [-l|-w|-c] [ARQUIVO]` | `wc -l notas` | Conta linhas, palavras ou bytes. Sem opção mostra as três contagens. |
| `sort [-r] [ARQUIVO]` | `sort -r nomes` | Ordena linhas por bytes; `-r` inverte a ordem. |
| `uniq [-c] [ARQUIVO]` | `uniq -c nomes` | Junta linhas adjacentes iguais; `-c` mostra a quantidade. |
| `tee [-a] ARQUIVO` | `comando | tee saída` | Copia a entrada para a saída e para um arquivo. `-a` acrescenta. |

## Rede e transferências

| Comando | Uso principal | Explicação |
|---|---|---|
| `ifconfig` | `ifconfig` | Mostra link, IPv4, máscara, gateway, DNS e contadores da interface Intel 82540EM. |
| `dhclient` | `dhclient` | Reinicia a configuração DHCP. Exige root. |
| `nslookup HOST` | `nslookup example.com` | Resolve um nome via DNS e mostra o endereço IPv4. |
| `ping [-c N] HOST` | `ping -c 3 gateway` | Envia pedidos ICMP e mostra respostas, tempo e resumo. O padrão é quatro pacotes. |
| `fetch [-o ARQUIVO|-] URL` | `fetch -o página https://example.com/` | Baixa HTTP ou HTTPS. HTTPS valida certificado, hostname e datas; a saída é limitada a 65536 bytes e a substituição do arquivo é atômica. |

## Contas e identidade

| Comando | Uso principal | Explicação |
|---|---|---|
| `whoami` | `whoami` | Mostra o nome da conta atual. |
| `id` | `id` | Mostra UID, nome e GID da conta atual. |
| `users` | `users` | Lista as contas cadastradas. |
| `passwd [NOME]` | `passwd` | Altera a senha própria; root também pode alterar a senha de outra conta. |
| `useradd NOME` | `useradd ana` | Cria uma conta e seu diretório pessoal. Exige root. |
| `userdel NOME` | `userdel ana` | Remove uma conta conforme as verificações do sistema. Exige root. |

O banco atual comporta até oito contas. Alterações tornam-se persistentes
depois de `sync` em uma instalação em disco.

## Administração e informações do sistema

| Comando | Uso principal | Explicação |
|---|---|---|
| `sync` | `sync` | Grava uma nova geração dos snapshots verificados do dekHFS. |
| `install ada0` | `install ada0` | Instala o dekH no disco IDE primário. Deve ser usado somente em disco descartável. |
| `shutdown -p now` | `shutdown -p now` | Sincroniza, se necessário, e solicita o desligamento da VM. |
| `reboot` | `reboot` | Sincroniza e reinicia a máquina. |
| `clear` | `clear` | Limpa a tela. |
| `uname [-s|-r|-m|-a]` | `uname -a` | Mostra nome do sistema, versão e arquitetura. |
| `mem` | `mem` | Mostra estado da execução protegida, frames livres e descritores abertos. |
| `mount` | `mount` | Mostra se `/` usa dekHFS volátil ou snapshots no `ada0`. Montar outros sistemas não é suportado. |
| `diskinfo` | `diskinfo` | Mostra a detecção e as características do disco IDE. |
| `keymap de|us` | `keymap de` | Seleciona o teclado alemão ou norte-americano. |
| `df` | `df` | Mostra entradas usadas, bytes armazenados, limite por arquivo e tipo de persistência. |
| `hostname [NOME]` | `hostname dekH-lab` | Mostra ou altera o hostname. A alteração é salva em `/etc/hostname`. |
| `date` | `date` | Mostra a data/hora obtida do relógio de hardware. |
| `uptime` | `uptime` | Mostra os segundos desde o boot. |

## Programas internos do boot

| Caminho | Explicação |
|---|---|
| `/sbin/init` | Processo inicial em ring 3. Reinicia o login quando uma sessão termina. |
| `/bin/login` | Autentica contas, configura `PATH`, `HOME`, `USER` e inicia `/bin/sh`. |

## Referência FreeBSD incorporada ao sistema-base

| Comando | Explicação |
|---|---|
| `bsdprobe` | ELF AMD64 FreeBSD 15 sem libc usado para validar a ABI FreeBSD, syscalls, arquivos, ambiente, memória anônima e TLS. A opção `--memory` executa a prova de memória. |

## Comandos FreeBSD da fixture experimental v4

Estes comandos são binários FreeBSD inalterados ou fixtures controladas. Eles
existem na VM descartável de desenvolvimento, não na VM principal da release.

### FreeBSD estático

“Estático” significa que o executável contém a libc necessária.

| Comando | Origem e função |
|---|---|
| `truebsd` | `/usr/bin/true` FreeBSD; termina com status 0. |
| `falsebsd` | `/usr/bin/false` FreeBSD; termina com status 1. |
| `bsdecho` | `echo` FreeBSD estático. |
| `bsdcat` | `cat` FreeBSD estático. |
| `bsdls` | `ls` FreeBSD estático. |
| `bsdmkdir` | `mkdir` FreeBSD estático. |
| `bsdrm` | `rm` FreeBSD estático. |
| `bsddyn` | Pequeno PIE de teste com `PT_INTERP`; valida o handoff, a pilha e o vetor auxiliar. |

### FreeBSD dinâmico

“Dinâmico” significa que o programa é carregado pelo `ld-elf.so.1` real e usa
as bibliotecas compartilhadas FreeBSD instaladas na fixture.

| Comando | Origem e função |
|---|---|
| `dynecho` | `echo` FreeBSD dinâmico. |
| `dyncat` | `cat` FreeBSD dinâmico. |
| `dynls` | `ls` FreeBSD dinâmico; também usa `libutil` e `libtinfow`. |
| `dynmkdir` | `mkdir` FreeBSD dinâmico. |
| `dynrm` | `rm` FreeBSD dinâmico. |
| `dyncp` | `cp` FreeBSD dinâmico. |
| `dynmv` | `mv` FreeBSD dinâmico. |
| `dynpwd` | `pwd` FreeBSD dinâmico. |
| `dynsleep` | `sleep` FreeBSD dinâmico, usando o `nanosleep` compatível. |
| `dynprintf` | `printf` FreeBSD dinâmico. |

## Console de recuperação

`rescue` sai do shell de usuário e abre o console root do kernel após a
autorização da sessão. Ele oferece versões administrativas diretas de vários
comandos já descritos e mais estes comandos específicos:

| Comando | Explicação |
|---|---|
| `dmesg` | Mostra um resumo fixo do boot, kernel, ABI e disco. |
| `stat CAMINHO` | Mostra tipo, modo, UID, GID e tamanho de uma entrada. |
| `run PROGRAMA [ARGUMENTOS]` | Executa explicitamente um ELF existente. Nomes sem `/` são procurados em `/bin`. |
| `halt` | Sincroniza quando necessário e solicita o desligamento, como o caminho de `shutdown`. |
| `pkg` | Informa explicitamente que o gerenciador de pacotes ainda não está disponível; não instala nada. |
| `man limitations` | Mostra `/root/README`, com os limites atuais do sistema. |

O console também oferece diretamente `help`, `mem`, `keymap`, `clear`,
`whoami`, `id`, `pwd`, `uname`, `hostname`, `date`, `uptime`, `diskinfo`, `df`,
`mount`, `sync`, `install`, `reboot`, `shutdown`, `echo`, `ls`, `cd`, `cat`,
`cp`, `mv`, `chmod`, `mkdir`, `touch`, `rm` e `rmdir`.

## Executáveis de diagnóstico e desenvolvimento

Os programas abaixo são semeados no sistema de desenvolvimento para testar o
kernel. Eles não devem ser tratados como ferramentas normais de usuário;
alguns provocam faults de propósito.

| Comando | Finalidade |
|---|---|
| `hello` | Confirma execução de um programa C em ring 3. |
| `fault` | Tenta escrever no texto do kernel; o processo deve ser encerrado sem derrubar o kernel. |
| `badptr` | Valida ponteiros, tamanhos, descritores e syscalls inválidas. |
| `privileged` | Tenta executar `cli` em ring 3 para testar proteção de instruções privilegiadas. |
| `spin` | Entra em loop para validar o limite de três segundos de CPU. |
| `textwrite` | Tenta escrever no segmento de código somente leitura. |
| `stackexec` | Tenta executar código na pilha NX. |
| `bss` | Valida BSS zerado e dados graváveis. |
| `iotest` | Testa abertura, leitura, offsets, descritores e limites da ABI de I/O. |
| `ttyread` | Testa leitura não bloqueante do terminal. |
| `ucat` | Implementação de diagnóstico do caminho de leitura/cópia usado por `cat`. |
| `argtest` | Valida `argc`, `argv`, aspas, argumentos vazios e limites. |
| `ucp` | Implementação de diagnóstico do caminho de cópia usado por `cp`. |
| `writetest` | Testa escrita, criação, truncamento, append e erros de acesso. |
| `copycheck A B` | Compara dois arquivos byte a byte. |
| `uexec PROGRAMA [ARGS]` | Substitui a imagem atual usando `exec`. |
| `exectest` | Testa atomicidade de falhas, argumentos e herança de descritores no `exec`. |
| `execinherit` | Verifica offsets herdados e `close-on-exec`. |
| `execoom` | Testa rollback de `exec` quando os frames acabam. |
| `execchain [N]` | Executa uma cadeia de até vinte substituições de imagem. |
| `childtest` | Testa spawn, status, isolamento, faults e recuperação de recursos de filhos. |
| `uspawn PROGRAMA [ARGS]` | Executa um filho, aguarda e mostra seu status. |
| `waittest` | Testa leitura bloqueante, retomada e interrupção por Ctrl-C. |
| `depthtest` | Valida o limite de profundidade/quantidade de processos. |
| `cwdtest` | Testa isolamento e herança do diretório atual entre pai, filho e exec. |
| `budgettest` | Valida a renovação do orçamento de CPU durante esperas de terminal. |
| `duptest` | Testa `dup`, streams padrão, herança, rollback e `close-on-exec`. |
| `streams` | Escreve mensagens separadas em stdout e stderr. |
| `pipetest` | Testa pipes, concorrência, bloqueio, preempção e memória privada. |
| `storagecheck` | Executa verificações públicas de arquivos grandes, limites e persistência dekHFS. |

## Limites importantes

- O sistema-base usa no máximo 256 entradas no dekHFS.
- Cada arquivo do dekHFS v4 pode ter até 2 MiB; o pool compartilhado possui
  16 MiB.
- Editor, download, utilitários de texto e substituição atômica continuam
  limitados a 64 KiB por operação/arquivo conforme o comando.
- Os comandos FreeBSD experimentais não significam compatibilidade geral com
  pacotes FreeBSD.
- `pkg` e Vim ainda não estão disponíveis.
