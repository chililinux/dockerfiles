# Usar a imagem base do Void Linux
FROM voidlinux/voidlinux:latest

# Configurar repositórios confiáveis e atualizar o sistema
RUN echo "repository=https://repo-fastly.voidlinux.org/current" > /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/multilib" >> /etc/xbps.d/00-repository-main.conf && \
    echo "repository=https://repo-fastly.voidlinux.org/current/multilib/nonfree" >> /etc/xbps.d/00-repository-main.conf && \
    xbps-install -Suy xbps

# Instalar pacotes essenciais
RUN xbps-install -y vim curl git bash xtools

# Definir o diretório de trabalho
WORKDIR /root

# Comando padrão quando o container for iniciado
CMD ["/bin/bash"]
