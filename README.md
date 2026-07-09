# mac-clean

Skill de Claude Code: auditor y limpiador de archivos basura del Mac.

Escanea cachés, logs y residuos de apps que ralentizan el ordenador, muestra un informe con tamaños exactos, espera confirmación explícita del usuario, y solo entonces limpia lo aprobado.

## Instalación

Copia `SKILL.md` a tu carpeta de skills de Claude Code:

```bash
mkdir -p ~/.claude/skills/mac-clean
cp SKILL.md ~/.claude/skills/mac-clean/
```

## Uso

Invócala diciendo "limpia el mac", "el ordenador va lento", "libera espacio", o con `/mac-clean`.

## Reglas de seguridad

- Nunca borra nada sin confirmación explícita del usuario.
- Papelera, Xcode Archives y volúmenes de Docker requieren confirmación aparte (no son caché regenerable).
- Nunca toca carpetas de proyectos ni datos de usuario.
