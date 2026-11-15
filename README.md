# ⏰ Reachy MCP keepalive (GitHub Actions)

Cron **en la nube** que mantiene despierto el Space MCP
**https://huggingface.co/spaces/Alcides777/reachy-mcp-tool**
cada **12 horas**, sin depender de ningún PC.

## Qué hace

1. `GET /gradio_api/mcp/schema` con timeout de 90 s (hasta 3 intentos).
2. Si el Space está dormido (502/503/timeout) → abre su página para despertarlo y reintenta.
3. Publica un resumen en la pestaña **Summary** del run.
4. Cada **7 días** hace un commit pequeño (`.github/keepalive.txt`): GitHub desactiva los
   cron de repositorios públicos tras 60 días sin actividad, así que esto lo evita.

## Programación

| Cuándo | |
|---|---|
| Cron | `0 6,18 * * *` (06:00 y 18:00 **UTC**) |
| Manual | Actions → *MCP keepalive* → **Run workflow** |

GitHub puede retrasar unos minutos los cron en horarios de mucha carga (es normal).

## Verlo funcionando

- Pestaña **Actions** del repo → *MCP keepalive* → última ejecución.
- O por API:
  ```bash
  gh run list --repo Alcides777venezuela/reachy-mcp-keepalive
  gh run watch --repo Alcides777venezuela/reachy-mcp-keepalive
  ```

## Cambiar la frecuencia

Edita `.github/workflows/keepalive.yml`:

| Cada | cron |
|---|---|
| 6 h | `0 */6 * * *` |
| 12 h (actual) | `0 6,18 * * *` |
| cada 24 h | `0 6 * * *` |

## Notas

- El Space es **público** → no hace falta token ni secretos.
- Si algún día se hace privado, añade el secret `HF_TOKEN` y el header
  `Authorization: Bearer ${{ secrets.HF_TOKEN }}` en el `curl`.
- Esto no "tortura" la infraestructura: son ~2 peticiones cada 12 h.
