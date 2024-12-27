# Etapa base
FROM manjarolinux/base:latest AS base
LABEL maintainer="Vilmar Catafesta <vcatafesta@gmail.com>"
LABEL description="ChiliLinux Build Package/ISO"
LABEL license="MIT"
LABEL version="1.6"
LABEL imagename="build-iso"

# Etapa de construção
FROM scratch AS build
COPY --from=base / /

# Configurações e arquivos essenciais
COPY pacman.conf /etc/pacman.conf
COPY pacman.d /etc/pacman.d
COPY locale.gen /etc/
COPY locale.conf /etc/
COPY usr /usr

#ENV LANG=en_US.UTF-8
ENV LANG=pt_BR.UTF-8
ENV PATH="/usr/bin:${PATH}"

# Configuração inicial
RUN echo "chililinux-build-iso" > /etc/hostname && \
    echo 'Server = https://mirrors2.manjaro.org/stable/$repo/$arch' > /etc/pacman.d/mirrorlist

# Configuração de chaves e permissões
RUN pacman-key --init && \
    pacman-key --recv-keys \
      45EC44879815E2A6 \
      AECEEE84E52BBFAA9F1C9DF01EA0CEEEB09B44A3 \
      EA4F7B321A906AD9 \
      29C71CE612B57264 \
      A0D5A8312A83940ED8B04B0F4BAC871802E960F1 && \
    pacman-key --lsign-key \
      45EC44879815E2A6 \
      AECEEE84E52BBFAA9F1C9DF01EA0CEEEB09B44A3 \
      EA4F7B321A906AD9 \
      29C71CE612B57264 \
      A0D5A8312A83940ED8B04B0F4BAC871802E960F1 && \
    chmod 600 /etc/pacman.d/gnupg/gpg.conf /etc/pacman.d/gnupg/*gpg* && \
    chmod 700 /etc/pacman.d/gnupg

# Atualização e instalação de pacotes necessários
RUN pacman -Syyu --noconfirm --needed \
      archlinux-keyring \
      manjaro-keyring \
      biglinux-keyring \
      community-keyring \
      chili-keyring \
      community-livecd-key \
      chili-livecd-key --overwrite '*'

RUN pacman-key --init && \
    pacman-key --populate

RUN pacman -Sy --needed --noconfirm \
      base curl duf fakeroot git github-cli gnupg inetutils jq \
      libarchive manjaro-chrootbuild nano openssh openssl rsync \
      tmate tree wget zip shfmt pyenv \
      chili-skel chili-nano-config chili-utils chili-install \
      chili-fetch chili-clonedisk binutils yay

# Limpeza de pacotes e sistema
RUN pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg/* /usr/share/{doc,man,info} /var/log/* /tmp/* /var/tmp/* && \
    sed -i 's/#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && locale-gen

# Configuração adicional
RUN echo 'PACKAGER="Vilmar Catafesta <vcatafesta@gmail.com>"' >> /etc/makepkg.conf && \
    echo 'GPGKEY="A0D5A8312A83940ED8B04B0F4BAC871802E960F1"' >> /etc/makepkg.conf && \
    useradd -m builduser && \
    echo 'builduser:451960' | chpasswd && \
    echo 'builduser ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    useradd -m vcatafesta -G wheel,tty && \
    echo 'vcatafesta:451960' | chpasswd && \
    echo 'vcatafesta ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

RUN echo 'root:256' | chpasswd

# Copiar arquivos do /etc/skel para o /root
RUN cp -Rpa /etc/skel/. /root/ && \
    chown -R root:root /root

RUN chown root:root /usr/bin/sudo && \
    chown root:root /etc/sudo.conf && \
    chmod 4755 /usr/bin/sudo && \
    chmod 0644 /etc/sudo.conf

# Otimização e remoção de arquivos desnecessários
RUN rm -rf /usr/share/gtk-doc /usr/lib/python*/test /usr/lib/python*/site-packages/*/tests && \
    find /usr/lib -type f -name '*.py[co]' -delete -o -type d -name __pycache__ -delete && \
    find /usr/share/locale -mindepth 1 -maxdepth 1 ! -name 'en_US' -exec rm -r {} + && \
    rm -rf /var/lib/pacman/sync/* \
           /usr/share/{doc,man,installed-tests,licenses,xml} \
           /usr/lib/systemd/{catalog,user} \
           /usr/share/{pkgconfig,aclocal,bash-completion,vim,zsh} \
           /usr/include/* /usr/lib/*.a /usr/lib/*.la && \
    find /usr/share/i18n/locales ! -name 'en_*' -type f -delete && \
    find /usr/share/i18n/charmaps ! -name 'UTF-8.gz' -type f -delete && \
    find /usr/lib -type f \( -name '*.mo' -o -name '*.po' \) -delete

# Etapa final
FROM scratch AS release
COPY --from=build / /
USER builduser
WORKDIR /home/builduser
CMD ["/bin/bash"]
