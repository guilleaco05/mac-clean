# 🧹 mac-clean

<p align="center">
  <img src="https://img.shields.io/badge/Platform-macOS-black?style=for-the-badge&logo=apple" alt="macOS" />
  <img src="https://img.shields.io/badge/Compatibility-Claude%20Code%20%7C%20Antigravity-blue?style=for-the-badge&logo=anthropic" alt="AI Agent Compatibility" />
  <img src="https://img.shields.io/badge/Safety-100%25%20Audit%20First-success?style=for-the-badge" alt="Safe by Design" />
  <img src="https://img.shields.io/badge/License-MIT-purple?style=for-the-badge" alt="License" />
</p>

**`mac-clean`** es una skill modular diseñada para asistentes de IA en terminal (**Claude Code**, **Antigravity**, **Cursor** y afines). Audita, calcula con precisión el tamaño de cachés, logs y residuos de desarrollo en macOS, y **solo limpia tras tu confirmación explícita**.

---

## ⚡ Características Principales

- 🔍 **Auditoría Previa Sin Riesgo:** Mide y desglosa en tiempo real el espacio recuperable antes de tocar un solo byte.
- 🛡️ **Seguridad por Diseño (Safe-by-Design):** 
  - **Cachés regenerables:** Limpieza inmediata con confirmación (`npm`, `pip`, `Homebrew`, cachés de sistema).
  - **Archivos protegidos:** La Papelera, volúmenes de Docker y Xcode Archives requieren aprobación individual aislada.
  - **Protección de proyectos:** Nunca toca directorios de código, configuraciones críticas ni datos de usuario.
- 🛠️ **Enfocado en Desarrolladores:** Limpia logs de IDEs (VS Code, Cursor, Antigravity) y acumulaciones de herramientas de desarrollo.

---

## 🖥️ Ejemplo de Salida

```text
📊 Estado del Sistema: 17 GB libres (de 228 GB)
Ganancia estimada: ~2.8 GB

Elemento                              Tamaño     Tipo
-----------------------------------------------------------------
~/.npm (Caché de paquetes)            1.9 GB     Caché regenerable
~/Library/Caches/                     807 MB     Caché de usuario
Antigravity / IDE Logs                23 MB      Logs temporales
~/Library/Logs/                       7.9 MB     Logs de sistema

¿Procedo con la limpieza de las cachés puras? Responde OK para continuar.
```

---

## 🚀 Instalación Rápida

### 1. Para Claude Code
```bash
mkdir -p ~/.claude/skills/mac-clean
curl -fsSL https://raw.githubusercontent.com/guilleaco05/mac-clean/main/SKILL.md -o ~/.claude/skills/mac-clean/SKILL.md
```
*(O copia el archivo localmente si has clonado el repositorio)*:
```bash
mkdir -p ~/.claude/skills/mac-clean
cp SKILL.md ~/.claude/skills/mac-clean/
```

### 2. Para Google Antigravity / Gemini
```bash
mkdir -p ~/.gemini/config/skills/mac-clean
cp SKILL.md ~/.gemini/config/skills/mac-clean/
```

---

## 💬 Modo de Uso

Una vez instalada la skill, puedes invocarla en lenguaje natural con tu asistente de IA favorito:

- *"Limpia el Mac"*
- *"El ordenador va lento, haz una auditoría"*
- *"Libera espacio en disco"*
- `"/mac-clean"`

---

## 🔒 Matriz de Seguridad y Permisos

| Categoría | Elementos | Comportamiento |
|---|---|---|
| 🟢 **Caché Pura** | `~/.npm`, `pip`, `~/Library/Caches/`, `~/.cache/`, `brew` | Borrado seguro con confirmación global |
| 🟡 **Logs de IDEs** | Logs de Cursor, VS Code, Antigravity, Logs de usuario | Borrado de contenido manteniendo estructura |
| 🔴 **Datos Críticos** | `~/.Trash`, `Docker`, `Xcode Archives` | **Requiere confirmación explícita 1 a 1** |
| ⛔ **Zonas Prohibidas** | Código fuente, `~/Documents`, entornos virtuales activos | **Intocables por política** |

---

## 👤 Autor

Desarrollado por **Guillermo Acosta Saborido**
- GitHub: [@guilleaco05](https://github.com/guilleaco05)
- LinkedIn: [guillermo-acosta-saborido](https://www.linkedin.com/in/guillermo-acosta-saborido)

## 📄 Licencia

Este proyecto está bajo la Licencia [MIT](LICENSE).
