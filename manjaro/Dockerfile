FROM manjarolinux/base:latest AS base

LABEL version="1.5.1"
LABEL description="ChiliLinux Build Package/ISO"
LABEL maintainer="Vilmar Catafesta <vcatafesta@gmail.com>"

# Squashing the whole base image into one layer
FROM scratch AS build
COPY --from=base / /

COPY pacman.conf /etc/pacman.conf

ENV LANG=en_US.UTF-8
ENV PATH="/usr/bin:${PATH}"

RUN uname -m \
    && pacman-key --init \
    && pacman-key --populate archlinux manjaro \
    && echo 'Server = https://mirrors2.manjaro.org/stable/$repo/$arch' > /etc/pacman.d/mirrorlist \
    && pacman-key -r 45EC44879815E2A6 \
    && pacman-key --lsign-key 45EC44879815E2A6 \
    && pacman-key --recv-keys AECEEE84E52BBFAA9F1C9DF01EA0CEEEB09B44A3 \
    && pacman-key --lsign-key AECEEE84E52BBFAA9F1C9DF01EA0CEEEB09B44A3 \
    && pacman-key --recv-keys EA4F7B321A906AD9 \
    && pacman-key --lsign-key EA4F7B321A906AD9 \
    && pacman-key --recv-keys 29C71CE612B57264 \
    && pacman-key --lsign-key 29C71CE612B57264 \
    && chmod 600 /etc/pacman.d/gnupg/gpg.conf \
    && chmod 600 /etc/pacman.d/gnupg/*gpg* \
    && chmod 700 /etc/pacman.d/gnupg \
    && pacman -Syyu --noconfirm --needed \
        archlinux-keyring \
        manjaro-keyring \
        biglinux-keyring \
        community-keyring \
        community-livecd-key \
    && pacman-key --populate archlinux manjaro \
    && pacman -Sy \
    && pacman -S --needed --noconfirm \
        base \
        curl \
        duf \
        fakeroot \
        git \
        github-cli \
        gnupg \
        inetutils \
        jq \
        libarchive \
        manjaro-chrootbuild \
        nano \
        openssh \
        openssl \
        rsync \
        tmate \
        tree \
        shfmt \
        wget \
        zip \
    && pacman -Scc --noconfirm \
    && rm -rf /var/cache/pacman/pkg/* \
    && rm -rf /usr/share/{doc,man,info} \
    && rm -rf /var/log/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/* \
    && sed -i 's/#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen \
    && locale-gen \
    && echo 'PACKAGER="Vilmar Catafesta <vcatafesta@gmail.com>"' >> /etc/makepkg.conf \
    && useradd -m builduser \
    && echo 'builduser ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
    && rm -rf /usr/share/gtk-doc \
    && rm -rf /usr/lib/python*/test \
    && rm -rf /usr/lib/python*/site-packages/*/tests \
    && find /usr/lib -type f -name '*.py[co]' -delete -o -type d -name __pycache__ -delete \
    # Additional cleanup steps
    && find /usr/share/locale -mindepth 1 -maxdepth 1 ! -name 'en_US' -exec rm -r {} + \
    && rm -rf /var/lib/pacman/sync/* \
    && rm -rf /usr/share/doc \
    && rm -rf /usr/share/man \
    && rm -rf /usr/include/* \
    && rm -rf /usr/lib/*.a \
    && rm -rf /usr/lib/*.la \
    && rm -rf /usr/lib/pkgconfig \
    && rm -rf /usr/share/pkgconfig \
    && rm -rf /usr/share/aclocal \
    && rm -rf /usr/share/bash-completion \
    && rm -rf /usr/share/vim \
    && rm -rf /usr/share/zsh \
    && rm -rf /usr/share/installed-tests \
    && rm -rf /usr/share/licenses \
    && rm -rf /usr/share/xml \
    && rm -rf /usr/lib/systemd/catalog \
    && rm -rf /usr/lib/systemd/user \
    && find /usr/share/i18n/locales ! -name 'en_*' -type f -delete \
    && find /usr/share/i18n/charmaps ! -name 'UTF-8.gz' -type f -delete \
    && find /usr/lib -type f \( -name '*.mo' -o -name '*.po' \) -delete

# Final docker image 
FROM scratch AS release
COPY --from=build / /
USER builduser
WORKDIR /home/builduser
CMD ["/bin/bash"]
