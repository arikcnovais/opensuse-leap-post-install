# Guia de Pós-Instalação: openSUSE Leap 16.1

Este é um script e guia prático de pós-instalação para o **openSUSE Leap 16.1** (com ambiente KDE Plasma ou GNOME), focado em fluxos de trabalho para edição de vídeo, fotografia, design gráfico, produtividade e jogos.

## 1. Atualização do Sistema e Repositório Packman

No openSUSE Leap, utiliza-se `zypper update` (ou `zypper up`) para atualizar os pacotes. O comando `zypper dup` é reservado para atualizações de versão ou distribuições de desenvolvimento como o openSUSE Tumbleweed.

O repositório **Packman** é essencial no openSUSE para obter codecs multimídia completos (como FFMPEG desrestrito).

```
# Atualizar a lista de repositórios e o sistema
sudo zypper refresh && sudo zypper update -y

# Adicionar o repositório Packman para o Leap 16.1
sudo zypper addrepo -f https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Leap_16.1/ packman

# Atualizar a lista de pacotes
sudo zypper refresh

# Mudar prioridade do repositório Packman para fornecer codecs nativos
sudo zypper install -y --from packman ffmpeg gstreamer-plugins-bad gstreamer-plugins-ugly gstreamer-plugins-libav
```

## 2. Configuração de Rede e Firewall (Firewalld)

O openSUSE utiliza o `firewalld` como padrão. Execute os comandos abaixo para liberar as portas necessárias para aplicativos de integração e compartilhamento de rede (KDE Connect, Samba, Warpinator e Touch Portal):

```
# Garantir que o firewalld está ativo e em execução
sudo systemctl enable --now firewalld

# Compartilhamento de Arquivos na Rede (Samba)
sudo firewall-cmd --permanent --add-service=samba

# KDE Connect
sudo firewall-cmd --permanent --add-port=1714-1764/tcp
sudo firewall-cmd --permanent --add-port=1714-1764/udp

# Touch Portal
sudo firewall-cmd --permanent --add-port=12135/tcp

# Warpinator
sudo firewall-cmd --permanent --add-port=42000-42001/tcp
sudo firewall-cmd --permanent --add-port=42000-42001/udp

# Recarregar as regras para aplicar as alterações
sudo firewall-cmd --reload
```

## 3. Instalação de Drivers NVIDIA

O openSUSE disponibiliza um repositório oficial mantido para os drivers proprietários da NVIDIA.

```
# Adicionar o repositório oficial da NVIDIA para a versão Leap 16.1
sudo zypper addrepo --refresh https://download.nvidia.com/opensuse/leap/16.1/ nvidia

# Atualizar base de repositórios
sudo zypper refresh

# Instalar o driver da GPU e o kit de suporte a aceleração CUDA
sudo zypper install -y nvidia-gfxG06-kmp-default x11-video-nvidiaG06 nvidia-computeG06 nvidia-utilsG06 cuda-toolkit
```

## 4. Suporte a Flatpak e Flathub

Habilite a integração completa com o Flathub para instalar softwares isolados com facilidade.

```
# Instalar suporte a flatpak e integração com a Central de Software (Discover)
sudo zypper install -y flatpak plasma-discover-backend-flatpak

# Adicionar o repositório remoto Flathub
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

## 5. Ferramentas Utilitárias e Nativas (Zypper)

Pacotes utilitários de sistema e terminal via gerenciador nativo `zypper`:

```
sudo zypper install -y vim btop fish tmux yt-dlp aria2
```

## 6. Aplicativos via Flatpak (Criação, Multimídia e Navegadores)

### Ferramentas Gráficas e Edição

```
flatpak install -y org.gimp.GIMP com.obsproject.Studio org.inkscape.Inkscape org.shotcut.Shotcut
```

### Navegadores Web

```
flatpak install -y com.google.Chrome com.microsoft.Edge org.mozilla.firefox app.zen_browser.zen
```

### Produtividade e Utilitários Gerais

```
flatpak install -y md.obsidian.Obsidian org.onlyoffice.desktopeditors com.usebottles.bottles com.github.tchx84.Flatseal org.gnome.Boxes io.missioncenter.MissionCenter com.dec05eba.gpu_screen_recorder fr.handbrake.ghb io.github.celluloid_player.Celluloid
```

## 7. Instalação e Ajustes do DaVinci Resolve

Baixe o instalador oficial `.run` no site da Blackmagic Design.

### 1. Dependências no openSUSE:

```
sudo zypper install -y libXinerama1 libXcomposite1 libXcursor1 libxcb-cursor0 fuse2
```

### 2. Executar o Instalador Ignorando Checagem de Distro:

```
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_*.run
```

### 3. Correção de Conflito de Bibliotecas Internas (Se necessário):

Caso o programa feche sozinho ao iniciar devido a bibliotecas duplicadas de sistema:

```
# Backup preventivo das bibliotecas internas
tar -cvhzf ~/backup-libs-resolve.tar.gz /opt/resolve/libs/libgmodule-2.0.so* /opt/resolve/libs/libglib-2.0.so* /opt/resolve/libs/libgio-2.0.so*

# Remoção para forçar o uso das bibliotecas nativas do openSUSE
sudo rm /opt/resolve/libs/libgmodule-2.0.so*
sudo rm /opt/resolve/libs/libglib-2.0.so*
sudo rm /opt/resolve/libs/libgio-2.0.so*
```

## 8. Ecossistema para Jogos (Flatpak)

```
flatpak install -y com.valvesoftware.Steam com.valvesoftware.Steam.Utility.vkBasalt com.heroicgameslauncher.hgl com.github.Matoking.protontricks io.github.radiolamp.mangojuice org.vinegarhq.Sober
```

## DICA EXTRA: Drivers Wi-Fi Broadcom

Se o seu computador possui uma placa de rede Wi-Fi da **Broadcom** (como a Broadcom BCM43xx / `broadcom-wl`) que não funcionou out-of-the-box, você pode adicionar o repositório oficial de hardware para a versão 16.1 do openSUSE e instalar os drivers compilados:

```
# Adicionar o repositório de Hardware do openSUSE Leap 16.1
sudo zypper addrepo -f https://download.opensuse.org/repositories/hardware/16.1/ hardware-16.1

# Atualizar a lista de pacotes
sudo zypper refresh

# Instalar o driver Broadcom Wireless (broadcom-wl e módulo KMP correspondente ao kernel)
sudo zypper install -y broadcom-wl broadcom-wl-kmp-default
```

Após a instalação do driver Broadcom, reinicie o computador para carregar o módulo do kernel.