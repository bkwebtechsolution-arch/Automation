# PDF conversion optimization notes

## Files changed
- `app.py`: configurable conversion timeout; bounded retries for transient HTTP 502/503/504 and network errors; includes short upstream response details in the error; logs conversion duration and output size.
- `pdf-service/Dockerfile`: uses `gotenberg/gotenberg:8-libreoffice` because this app only calls `/forms/libreoffice/convert`; enables LibreOffice auto-start and adds conservative startup/queue/restart settings.
- `pdf-service/README.md`: deployment, health-check, and configuration instructions.

## Files intentionally unchanged
All five DOCX templates, HTML templates, CSS, and JavaScript are preserved byte-for-byte. The existing form fields, dropdown options, text, document mapping, and document-generation/replacement logic are unchanged.

## Flask service environment variables
- `GOTENBERG_URL`: required; base URL only, e.g. `https://your-gotenberg-service.example.com`
- `GOTENBERG_TIMEOUT_SECONDS=180`: optional, conversion request timeout.
- `GOTENBERG_MAX_ATTEMPTS=2`: optional; maximum is clamped to 3.

## Deploy the PDF service
1. Push the updated repository to your Git provider.
2. In Render, create/update a Docker web service using root directory `Automation-main/pdf-service` (or the equivalent directory containing the Dockerfile in your repository).
3. Set the service's internal port to 3000 if requested.
4. Wait for deployment to finish. Open `https://YOUR-GOTENBERG-HOST/health`; expect HTTP 200.
5. In the Flask app service's Environment settings, set `GOTENBERG_URL` to the Gotenberg base URL. Do not include `/forms/libreoffice/convert`.
6. Redeploy/restart the Flask service after changing the environment variable.

## Free hosting expectations
Render Free web services sleep after 15 minutes without inbound traffic and can take about a minute to wake. Therefore cold-start delay and occasional upstream 502s can still occur on free hosting. Free instances are suitable for testing, not dependable production conversion. A small container needs at least 512 MiB RAM and 0.2 CPU per Gotenberg installation guidance; if the platform's free CPU/RAM is below this, large DOCX files may be slow or fail. A paid instance or a small always-on VPS is the reliability option.

## Validation performed
- Python syntax compilation passed for `app.py`.
- ZIP integrity and template/UI byte comparisons should be performed after download/deploy.
- A live Gotenberg endpoint and production Render deployment were not available from this workspace, so live conversion and formatting equivalence have not been claimed. Test all five generated PDFs against the originals before production use.
