# PDF conversion service (Gotenberg 8 + LibreOffice)

This service converts the existing generated DOCX files to PDF. It does not edit
or replace the Word templates.

## Deployment

Deploy this directory (`pdf-service/`) as a Docker web service on a container
platform. Gotenberg listens on port `3000`; configure the platform's internal
port as `3000` if it asks.

After deployment, set this environment variable on the Flask application:

`GOTENBERG_URL=https://YOUR-ACTUAL-GOTENBERG-HOST`

Use the base URL only: do not add `/forms/libreoffice/convert` to the variable.
The Flask app appends that endpoint automatically.

Optional Flask application settings:
- `GOTENBERG_TIMEOUT_SECONDS=180` (default 180)
- `GOTENBERG_MAX_ATTEMPTS=2` (default 2; maximum allowed is 3)

## Performance configuration

- `LIBREOFFICE_AUTO_START=true` starts LibreOffice at service startup, reducing
  first-conversion latency while the service is awake.
- `LIBREOFFICE_START_TIMEOUT=60s` allows a slower instance to start.
- `LIBREOFFICE_RESTART_AFTER=20` reduces frequent process restarts versus the
  default while retaining periodic restarts. If memory usage grows, lower it.
- `LIBREOFFICE_MAX_QUEUE_SIZE=2` prevents an unlimited backlog on a small
  instance. Under concurrent load, excess requests may be rejected rather than
  waiting indefinitely.
- `LIBREOFFICE_IDLE_SHUTDOWN_TIMEOUT=0` avoids LibreOffice idling out while the
  container itself remains awake.

These settings are starting points, not guarantees. Gotenberg/LibreOffice needs
at least 512 MiB RAM and 0.2 CPU according to its installation guidance; more
memory/CPU generally helps larger documents. Test the actual templates after
deployment.

## Health check

Open `https://YOUR-ACTUAL-GOTENBERG-HOST/health`. A healthy service should return
HTTP 200. This checks service health, not a complete DOCX conversion.

## Important

Do not expose an unauthenticated Gotenberg instance to the public internet if
you can avoid it. Restrict access with platform private networking or an
appropriate access-control layer. If Flask and Gotenberg are hosted separately,
ensure the Flask service can reach the Gotenberg URL.
