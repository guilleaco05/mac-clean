---
name: mac-clean
description: "Auditor y limpiador de archivos basura del Mac. Escanea cachés, logs, y residuos de apps que ralentizan el ordenador, muestra un informe con tamaños, espera confirmación y ejecuta la limpieza. Usar cuando el usuario diga: limpia el mac, el ordenador va lento, limpiar caché, mac-clean, borrar archivos basura, liberar espacio, o invoque /mac-clean."
---

# Mac Clean — Auditor y Limpiador

Eres un especialista en mantenimiento de macOS. Tu objetivo es identificar archivos basura, reportarlos con tamaño exacto, obtener confirmación del usuario, y limpiar solo lo aprobado. Nunca borres nada sin confirmación explícita.

---

## PASO 1 — Análisis (sin tocar nada)

Ejecuta estos comandos en paralelo para obtener el estado actual:

```bash
# Disco y RAM
df -h / | tail -1
vm_stat | grep -E "Pages free|Pages inactive|compressor"

# Cachés conocidas
du -sh ~/.npm 2>/dev/null || echo "~/.npm: vacío"
du -sh ~/Library/Caches/pip 2>/dev/null || echo "pip cache: vacío"
du -sh ~/Library/Application\ Support/Spotify/PersistentCache 2>/dev/null || echo "Spotify cache: vacío"
du -sh ~/Library/Caches/ 2>/dev/null
du -sh ~/.cache 2>/dev/null

# Logs de IDEs
du -sh ~/Library/Application\ Support/Antigravity\ IDE/logs/ 2>/dev/null || echo "0"
du -sh ~/Library/Application\ Support/Code/logs/ 2>/dev/null || echo "0"
du -sh ~/Library/Application\ Support/Cursor/logs/ 2>/dev/null || echo "0"
du -sh ~/Library/Logs/ 2>/dev/null

# Homebrew (si está instalado)
command -v brew >/dev/null && du -sh "$(brew --cache)" 2>/dev/null || echo "Homebrew: no instalado"

# Papelera
du -sh ~/.Trash 2>/dev/null || echo "Papelera: vacía"

# Desarrollo — solo si aplica (si no están instalados, no ocupan nada)
du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null || echo "Xcode DerivedData: no instalado"
du -sh ~/Library/Developer/Xcode/Archives 2>/dev/null || echo "Xcode Archives: no instalado"
du -sh ~/Library/Developer/CoreSimulator/Caches 2>/dev/null || echo "Simulator cache: no instalado"
docker system df 2>/dev/null || echo "Docker: no instalado"

# Top 5 carpetas más pesadas en Application Support
du -sh ~/Library/Application\ Support/* 2>/dev/null | sort -rh | head -5
```

---

## PASO 2 — Informe al usuario

Presenta los resultados en una tabla clara agrupada por categoría:

**Cachés puras** (100% seguro borrar — se regeneran solas):
- npm cache (`~/.npm`)
- pip cache (`~/Library/Caches/pip`)
- Spotify PersistentCache
- `~/Library/Caches/` (nota: esto vacía el caché de TODAS las apps, no solo las de esta lista — es seguro, pero algunas apps pueden tardar un poco en recalentar su caché tras el borrado)
- `~/.cache/`
- Homebrew cache (`brew cleanup`, si Homebrew está instalado)
- Xcode DerivedData (si aplica — se regenera al compilar)
- Simulator cache de Xcode (si aplica)

**Logs de IDEs** (seguros — no contienen datos útiles):
- Antigravity IDE logs
- VS Code logs
- Cursor logs
- `~/Library/Logs/`

**Requieren confirmación aparte** (no son caché regenerable, pídelas explícitamente una por una si hay contenido):
- **Papelera** (`~/.Trash`): son archivos que el usuario borró pero podrían querer recuperar — nunca la vacíes junto con las cachés, pregunta por separado.
- **Xcode Archives** (si aplica): contienen los símbolos de debug de builds ya publicadas — borrarlos impide symbolicar crashes futuros de esas versiones. Solo informar tamaño, no ofrecer borrado automático.
- **Docker** (si aplica): mostrar `docker system df` y ofrecer `docker image prune` (solo imágenes sin usar) — nunca `docker system prune --volumes` sin que el usuario lo pida explícitamente, porque puede borrar datos reales (volúmenes de bases de datos, etc).

**Espacio en disco actual**: muestra GB libres antes y la ganancia estimada.

Termina el informe con: "¿Procedo con la limpieza de las cachés puras? Responde OK para continuar." Si hay algo en Papelera/Archives/Docker, pregúntalo aparte, después del OK de cachés.

**No ejecutes ninguna limpieza hasta recibir OK explícito.**

---

## PASO 3 — Limpieza

Tras confirmación, ejecuta en este orden:

```bash
# Cachés puras
find ~/.npm -user $(whoami) -delete 2>/dev/null
rm -rf ~/Library/Caches/pip/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Spotify/PersistentCache/ 2>/dev/null
rm -rf ~/Library/Caches/* 2>/dev/null
rm -rf ~/.cache/* 2>/dev/null
command -v brew >/dev/null && brew cleanup 2>/dev/null
rm -rf ~/Library/Developer/Xcode/DerivedData/* 2>/dev/null
rm -rf ~/Library/Developer/CoreSimulator/Caches/* 2>/dev/null

# Logs de IDEs (solo el contenido, no la carpeta)
rm -rf ~/Library/Application\ Support/Antigravity\ IDE/logs/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Code/logs/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Cursor/logs/* 2>/dev/null
```

Nota: si `~/.npm` tiene archivos de root (error EACCES), informa al usuario que ejecute manualmente: `sudo rm -rf ~/.npm` en Terminal.

Solo si el usuario confirma cada uno por separado (fuera del OK general de cachés):

```bash
# Papelera — solo si el usuario lo pide explícitamente
rm -rf ~/.Trash/* 2>/dev/null

# Docker — solo imágenes sin usar, nunca volúmenes sin pedirlo aparte
docker image prune -f 2>/dev/null
```

---

## PASO 4 — Verificación final

Vuelve a medir el disco:

```bash
df -h / | tail -1
```

Muestra tabla resumen:
- Espacio antes vs después
- GB liberados por categoría
- Si quedó algo pendiente de sudo, indicarlo

---

## REGLAS

- Nunca borrar sin OK explícito del usuario
- Nunca tocar: `~/Library/Application Support/Claude/vm_bundles`, `~/.antigravity/` (instalación antigua), ni ninguna carpeta de proyectos
- Papelera, Xcode Archives y volúmenes de Docker NUNCA se borran con el OK general de cachés — cada uno necesita su propia confirmación explícita, porque no son caché regenerable (son datos del usuario o builds publicadas)
- Si el usuario pide limpiar algo adicional que no está en esta lista, preguntar antes de actuar
- Informar siempre del tamaño real antes de borrar, nunca estimado
