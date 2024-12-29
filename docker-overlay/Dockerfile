FROM archlinux:latest

# Instale fuse-overlayfs
RUN pacman -Syu --noconfirm && \
    pacman -S --noconfirm \
        fuse-overlayfs \
        bash \
        util-linux \
        coreutils \
        sudo \
        git \
        ncurses \
        nano \
        && pacman -Scc --noconfirm

# Crie diretórios para o overlay
RUN mkdir -p /upper /work /lower /merged

# Comando padrão para manter o contêiner em execução e permitir testes
CMD [ "bash" ]
