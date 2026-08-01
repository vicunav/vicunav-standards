# Idioma de la documentación

Este estándar aplica a todos los repositorios del ecosistema Vicunav y define en qué idioma se escribe cada tipo de documento.

## Regla

- **README.md principal de cada repositorio público → inglés.** Es la superficie de descubrimiento: lo primero que ve alguien externo (reclutadores, colaboradores futuros, portafolio) al entrar al repositorio.
- **AGENTS.md, Constitución del proyecto, ADRs, todo dentro de `docs/`, comentarios de código → español.** Son documentos internos de trabajo, dirigidos a Codex y al responsable del ecosistema, no a un lector externo.

## Aplicación

- Un README.md que se traduce al inglés debe reflejar el estado real del repositorio verificado contra el código y la documentación actuales; no es una traducción mecánica del texto anterior.
- Los mensajes de commit nuevos se mantienen en español, salvo indicación distinta.
- Esta regla no afecta la descripción corta del repositorio en GitHub (el one-liner de la lista de repos), que también debe estar en inglés por ser parte de la misma superficie de descubrimiento.
