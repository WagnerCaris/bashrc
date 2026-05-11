# Dependências do repositório `bashrc`

Este arquivo lista os softwares, pacotes e serviços externos usados pelos arquivos de configuração do shell deste repositório.

Arquivos analisados:

- `.bashrc`
- `.10-bash_aliases`
- `.20-bash_functions`
- `.30-bash_services`
- `.40-bash_paths`

> Observação: alguns comandos são básicos do sistema GNU/Linux e normalmente já vêm instalados. Outros são opcionais e só são necessários para funções/aliases específicos.

---

## 1. Pacotes essenciais para carregar o ambiente

Estes são os pacotes mais importantes para que o `.bashrc`, aliases, funções e helpers funcionem corretamente.

### Debian / Ubuntu / Linux Mint

```bash
sudo apt update
sudo apt install -y \
  bash \
  bash-completion \
  coreutils \
  util-linux \
  findutils \
  grep \
  gawk \
  sed \
  procps \
  ncurses-bin \
  openssh-client \
  rsync \
  sudo \
  tar \
  gzip \
  bzip2 \
  xz-utils \
  unzip \
  p7zip-full \
  unrar \
  nmap \
  mc \
  micro \
  eza
```

### Observações

- `bash`: shell usado pelo `.bashrc`.
- `bash-completion`: usado quando o `.bashrc` carrega `/usr/share/bash-completion/bash_completion` ou `/etc/bash_completion`.
- `coreutils`: fornece comandos como `cat`, `df`, `du`, `sort`, `head`, `tr`, `column`, `dirname`, `readlink`, `ls`, `mkdir`, `rm`, `clear`, `hostname`, `uptime` em muitas distribuições.
- `util-linux`: em Debian/Ubuntu normalmente fornece `column` e `setsid`.
- `findutils`: fornece `find`.
- `grep`: necessário porque algumas funções chamam `/bin/grep` diretamente.
- `gawk`: usado em várias funções com `awk`.
- `sed`: usado em funções de inspeção de serviços.
- `procps`: fornece `ps`.
- `ncurses-bin`: fornece `tput`, usado para cores no terminal.
- `openssh-client`: fornece `ssh` e `scp`.
- `rsync`: usado pela função `rsync()`.
- `sudo`: usado por funções como `bigdirs`, `bigarchives`, `nmapscan` e inspeção de serviços.
- `tar`, `gzip`, `bzip2`, `xz-utils`, `unzip`, `p7zip-full`, `unrar`: usados pela função `extract()`.
- `nmap`: usado pela função `nmapscan()`.
- `mc`: usado pelo alias `mc`, que carrega `/usr/lib/mc/mc-wrapper.sh`.
- `micro`: editor definido por `EDITOR="micro"`.
- `eza`: usado pelos aliases `l` e `ll`.

---

## 2. Pacotes recomendados para produtividade no terminal

O arquivo de ajuda (`myhelp`) menciona ferramentas modernas para busca, navegação e inspeção. Elas não são todas chamadas diretamente pelas funções atuais, mas fazem parte do ambiente esperado.

```bash
sudo apt install -y \
  ripgrep \
  fd-find \
  bat \
  fzf \
  zoxide \
  direnv \
  tmux \
  pstree
```

### Observações por distribuição

Em Debian/Ubuntu, alguns binários podem ter nomes diferentes:

- `fd-find` instala o binário `fdfind`, não `fd`.
- `bat` pode instalar o binário `batcat`, não `bat`.
- `pstree` normalmente vem do pacote `psmisc`.

Instalação alternativa mais explícita:

```bash
sudo apt install -y ripgrep fd-find bat fzf zoxide direnv tmux psmisc
```

Links úteis para aliases locais:

```bash
mkdir -p ~/.local/bin
ln -sf /usr/bin/fdfind ~/.local/bin/fd
ln -sf /usr/bin/batcat ~/.local/bin/bat
```

Integração esperada do `fzf` no Bash:

```bash
eval "$(fzf --bash)"
```

Integração comum do `zoxide` no Bash:

```bash
eval "$(zoxide init bash)"
```

Integração comum do `direnv` no Bash:

```bash
eval "$(direnv hook bash)"
```

---

## 3. Serviços e ferramentas de systemd

As funções `services`, `servdebug` e `servinspect` dependem de systemd.

Pacotes normalmente necessários:

```bash
sudo apt install -y systemd systemd-sysv dbus journalctl
```

Em muitas distribuições baseadas em Debian, `journalctl` vem junto com `systemd`, então este comando pode variar:

```bash
sudo apt install -y systemd dbus
```

Comandos usados:

- `systemctl`
- `journalctl`
- `hostname`
- `uptime`
- `awk`
- `sed`
- `ps`
- `pstree` opcional em `servinspect`

Serviços citados nos exemplos:

- `ssh.service`
- `cron.service`
- `NetworkManager.service`
- `zerotier-one.service`

Se quiser usar todos os exemplos exatamente como aparecem:

```bash
sudo apt install -y openssh-server cron network-manager
```

Para ZeroTier:

```bash
curl -s https://install.zerotier.com | sudo bash
```

---

## 4. Aplicações gráficas chamadas por aliases

Os aliases abaixo usam a função `gui`, que chama `/usr/bin/setsid` e encerra o terminal:

- `firefox='gui firefox'`
- `brave='gui brave-browser'`
- `qgis='gui qgis'`
- `thunar='gui thunar'`
- `nautilus='gui nautilus'`
- `configuration='gui xfce4-settings-manager'`

Pacotes correspondentes:

```bash
sudo apt install -y \
  firefox \
  qgis \
  thunar \
  nautilus \
  xfce4-settings \
  util-linux
```

Brave normalmente é instalado pelo repositório oficial ou pacote `.deb` externo. Exemplo via repositório oficial:

```bash
sudo apt install -y curl
sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg \
  https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
printf 'deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main\n' \
  | sudo tee /etc/apt/sources.list.d/brave-browser-release.list
sudo apt update
sudo apt install -y brave-browser
```

---

## 5. Ambientes Python, Mamba e Micromamba

O `.bashrc` define:

```bash
export MAMBA_ROOT_PREFIX="$HOME/.local/share/mamba"
```

E carrega o hook do Micromamba se existir:

```bash
$HOME/.local/bin/micromamba shell hook --shell bash
```

Dependências relacionadas:

- `python3`
- `python3-pip`
- `python3-venv`
- `micromamba`

Instalação base:

```bash
sudo apt install -y python3 python3-pip python3-venv
```

Instalação do Micromamba em `~/.local/bin`:

```bash
mkdir -p ~/.local/bin
curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest \
  | tar -xvj -C /tmp bin/micromamba
mv /tmp/bin/micromamba ~/.local/bin/micromamba
rmdir /tmp/bin 2>/dev/null || true
```

---

## 6. CUDA, NVIDIA e GDL

O `.bashrc` adiciona caminhos relacionados a CUDA e NVIDIA:

```bash
export LD_LIBRARY_PATH="/usr/lib/x86_64-linux-gnu/nvidia/current${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}"
export PATH="/usr/local/cuda/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}"
```

Também define variáveis para GDL:

```bash
export GDL_STARTUP="/opt/os/home/proc/.gdl_startup.pro"
export GDL_PATH="/usr/local/share/gnudatalanguage/lib/"
```

Dependências possíveis:

```bash
sudo apt install -y gnudatalanguage
```

CUDA/NVIDIA depende da sua GPU, distribuição e versão do driver. Em Ubuntu, normalmente envolve:

- driver NVIDIA proprietário
- CUDA Toolkit
- bibliotecas NVIDIA em `/usr/lib/x86_64-linux-gnu/nvidia/current`

Verificações úteis:

```bash
command -v nvidia-smi
command -v nvcc
nvidia-smi
nvcc --version
```

---

## 7. Ferramentas FPGA mencionadas pela função `fpga()`

A função `fpga()` mostra ajuda para um fluxo de FPGA com simulação e waveform.

Dependências citadas:

```bash
sudo apt install -y \
  make \
  gtkwave \
  iverilog \
  verilator \
  ghdl \
  python3-venv \
  python3-pip
```

Comandos mencionados:

- `make`
- `gtkwave`
- `iverilog`
- `verilator`
- `ghdl`
- `pip`
- `python3 -m venv`

---

## 8. Arquivos e caminhos esperados no ambiente

Alguns caminhos são específicos do seu sistema e não são instalados automaticamente por pacotes padrão:

- `/opt/os/home/proc/.gdl_startup.pro`
- `/usr/local/share/gnudatalanguage/lib/`
- `/opt/os/app/bin`
- `/opt/os/usr/local/bin`
- `/opt/os/bin`
- `/opt/os/app/configs/mmi/mmi.config`
- `/container/proc`
- `~/Documentos`
- `~/Downloads`
- `~/fpga-lab`

Criação opcional de diretórios pessoais:

```bash
mkdir -p ~/Documentos ~/Downloads ~/fpga-lab ~/.local/bin ~/.local/share/mamba
```

---

## 9. Lista consolidada de comandos externos usados

Comandos chamados diretamente nos arquivos analisados:

```text
7z
awk
bunzip2
cat
clear
column
command
df
dirname
du
echo
eza
find
gunzip
gzip/head
hostname
journalctl
mkdir
nmap
pip
ps
pstree
python
readlink
rm
rsync
scp
sed
setsid
sort
ssh
sudo
systemctl
tar
tput
tr
uncompress
unrar
unxz
unzip
uptime
which
```

Comandos mencionados ou esperados pela ajuda:

```text
bat
batcat
brave-browser
direnv
fd
fdfind
firefox
fzf
ghdl
gtkwave
iverilog
make
mc
micro
nautilus
qgis
rg
tmux
verilator
xfce4-settings-manager
zoxide
```

---

## 10. Script rápido de verificação

Use este bloco para verificar o que falta no sistema atual:

```bash
for cmd in \
  bash tput dircolors column tr sudo du sort head awk find df grep scp rsync ssh \
  tar unzip unrar 7z gunzip bunzip2 unxz uncompress nmap systemctl journalctl \
  ps pstree eza micro mc rg fd fzf zoxide direnv tmux bat python pip make \
  gtkwave iverilog verilator ghdl firefox brave-browser qgis thunar nautilus xfce4-settings-manager
 do
  if command -v "$cmd" >/dev/null 2>&1; then
    printf 'OK      %s -> %s\n' "$cmd" "$(command -v "$cmd")"
  else
    printf 'FALTA   %s\n' "$cmd"
  fi
done
```

Versão interativa com `fzf` para filtrar os ausentes:

```bash
for cmd in \
  bash tput dircolors column tr sudo du sort head awk find df grep scp rsync ssh \
  tar unzip unrar 7z gunzip bunzip2 unxz uncompress nmap systemctl journalctl \
  ps pstree eza micro mc rg fd fzf zoxide direnv tmux bat python pip make \
  gtkwave iverilog verilator ghdl firefox brave-browser qgis thunar nautilus xfce4-settings-manager
 do
  command -v "$cmd" >/dev/null 2>&1 || printf '%s\n' "$cmd"
done | fzf --prompt='Comandos ausentes > '
```

---

## 11. Instalação sugerida em uma máquina Debian/Ubuntu nova

Instalação prática para cobrir quase tudo do ambiente de terminal:

```bash
sudo apt update
sudo apt install -y \
  bash bash-completion coreutils util-linux findutils grep gawk sed procps psmisc ncurses-bin \
  openssh-client rsync sudo tar gzip bzip2 xz-utils unzip p7zip-full unrar nmap mc micro eza \
  ripgrep fd-find bat fzf zoxide direnv tmux \
  python3 python3-pip python3-venv \
  systemd dbus \
  make gtkwave iverilog verilator ghdl \
  firefox qgis thunar nautilus xfce4-settings
```

Depois, compatibilidade com nomes modernos:

```bash
mkdir -p ~/.local/bin
[ -x /usr/bin/fdfind ] && ln -sf /usr/bin/fdfind ~/.local/bin/fd
[ -x /usr/bin/batcat ] && ln -sf /usr/bin/batcat ~/.local/bin/bat
```

E garanta que `~/.local/bin` esteja no `PATH`, como já está no seu `.bashrc`.
