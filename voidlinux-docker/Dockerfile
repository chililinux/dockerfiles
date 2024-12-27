# Etapa base
FROM voidlinux/voidlinux:latest
LABEL description="ChiliLinux Build Void Package/ISO"
LABEL maintainer="Vilmar Catafesta <vcatafesta@gmail.com>"
LABEL version="1.4"
LABEL imagename="voidlinux-docker"

# Configurações e arquivos essenciais
COPY etc/ /etc
COPY usr/ /usr
COPY keys/* /var/db/xbps/keys/

# Configurar repositórios confiáveis e atualizar o sistema
RUN echo "repository=https://repo-fastly.voidlinux.org/current" > /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/multilib" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/multilib/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://mirrors.servercentral.com/voidlinux/current" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://mirrors.servercentral.com/voidlinux/current/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://mirrors.servercentral.com/voidlinux/current/multilib" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://mirrors.servercentral.com/voidlinux/current/multilib/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://chililinux.com/void/current" >> /etc/xbps.d/00-repository-main.conf && \
    xbps-install --sync --update --yes xbps

# Instalar pacotes essenciais
RUN xbps-install -y xtools bash sudo openssh tmate xz zip p7zip jq git github-cli kmod ncurses which vpm vsv \
            libstdc++ nano curl tree duf gettext rsync util-linux coreutils sed grep btrfs-progs e2fsprogs \
            exfatprogs dosfstools xfsprogs exa pv dialog parted lvm2 cryptsetup fastfetch gnupg file

# Atualização geral
RUN xi -u --yes || true

# Remover pacotes obsoletos
RUN xbps-remove -oO -y

# Configuração adicional
RUN  useradd -m -G wheel,audio,video,cdrom,optical,kvm,xbuilder,users builduser && \
    echo 'builduser:451960' | chpasswd && \
    echo 'builduser ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    useradd -m -G wheel,audio,video,cdrom,optical,kvm,xbuilder vcatafesta && \
    echo 'vcatafesta:451960' | chpasswd && \
    echo 'vcatafesta ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    useradd -m -G wheel,audio,video,cdrom,optical,kvm,xbuilder anon && \
    echo 'anon:voidlinux' | chpasswd && \
    echo 'anon ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

# Conceda permissões sudo para builduser sem a necessidade de senha
RUN echo "builduser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

RUN echo 'root:voidlinux' | chpasswd && \
    echo 'root   ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    echo '%wheel ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    echo '%sudo  ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

# Altere o shell do root:
RUN chsh -s /usr/bin/bash root

# Copiar arquivos do /etc/skel para o /root
RUN cp -Rpa /etc/skel/. /root/ && \
    chown -R root:root /root

# Clonar o repositório no home do usuário builduser
RUN git clone --depth=1 https://github.com/void-linux/void-packages /home/builduser/void-packages && \
    chown -R builduser:builduser /home/builduser/void-packages && \
    chmod +s /home/builduser/void-packages/xbps-src && \
    su - builduser -c "cd /home/builduser/void-packages && ./xbps-src binary-bootstrap"

USER root
# Definir o diretório de trabalho
#WORKDIR /root
WORKDIR /root

# Comando padrão quando o container for iniciado
CMD ["/bin/bash"]
