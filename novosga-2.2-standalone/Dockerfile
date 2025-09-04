# Usamos a imagem oficial como base
FROM novosga/novosga:2.2.7

# Argumento que será recebido do EasyPanel durante o build
ARG VITE_MERCURE_URL

# Variáveis de ambiente
ENV APP_ENV=prod \
    LANGUAGE=pt_BR \
    VITE_MERCURE_URL=${VITE_MERCURE_URL} \
    NOVOSGA_ADMIN_USERNAME="admin" \
    NOVOSGA_ADMIN_PASSWORD="123456" \
    NOVOSGA_ADMIN_FIRSTNAME="Administrador" \
    NOVOSGA_ADMIN_LASTNAME="Global" \
    NOVOSGA_UNITY_NAME="Minha Unidade" \
    NOVOSGA_UNITY_CODE="U01" \
    NOVOSGA_NOPRIORITY_NAME="Normal" \
    NOVOSGA_NOPRIORITY_DESCRIPTION="Serviço normal" \
    NOVOSGA_PRIORITY_NAME="Prioridade" \
    NOVOSGA_PRIORITY_DESCRIPTION="Serviço prioritário" \
    NOVOSGA_PLACE_NAME="Guichê"

# Mudamos para o usuário root para poder instalar pacotes e alterar permissões
USER root

# Instala o supercronic para agendamento de tarefas
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.2.33/supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=71b0d58cc53f6bd72cf2f293e09e294b79c666d8 \
    SUPERCRONIC=supercronic-linux-amd64

RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic

# --- INÍCIO DA CORREÇÃO ---
# Força a reconstrução dos assets do frontend durante o build, usando o caminho correto.
RUN echo "Rebuilding frontend assets with VITE_MERCURE_URL=${VITE_MERCURE_URL}" && \
    /usr/src/novosga/bin/console vite:build
# --- FIM DA CORREÇÃO ---

# Copia os arquivos de configuração e o script de inicialização
COPY start.sh /usr/local/bin/
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY config/crontab /etc/crontab

# Garante que o script de inicialização seja executável
RUN chmod +x /usr/local/bin/start.sh

# Muda para um usuário não-privilegiado por segurança
USER nobody

# Define o comando padrão para iniciar a aplicação
CMD ["start.sh"]
