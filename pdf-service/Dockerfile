# DOCX/XLSX/PPTX conversion only: LibreOffice variant is smaller than the full
# Gotenberg image because this project does not use Chromium HTML-to-PDF routes.
FROM gotenberg/gotenberg:8-libreoffice

EXPOSE 3000

# Start LibreOffice during service startup instead of paying startup cost on
# the first conversion. Keep a modest queue and restart periodically for safety.
ENV LIBREOFFICE_AUTO_START=true \\
    LIBREOFFICE_START_TIMEOUT=60s \\
    LIBREOFFICE_RESTART_AFTER=20 \\
    LIBREOFFICE_MAX_QUEUE_SIZE=2 \\
    LIBREOFFICE_IDLE_SHUTDOWN_TIMEOUT=0 \\
    LOG_LEVEL=info
