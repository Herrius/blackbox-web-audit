# 12 - Workflow con tmux

tmux es el multiplexor de terminal para gestionar sesiones de auditoria. Permite mantener multiples herramientas corriendo en paralelo, organizar el flujo de trabajo por fases, y recuperar sesiones si se pierde la conexion.

## Tabla de Contenidos

- [Por que tmux para Pentesting](#por-que-tmux-para-pentesting)
- [Sesion de Auditoria - Layout](#sesion-de-auditoria---layout) — Script de creacion, Layout visual
- [Comandos tmux Esenciales](#comandos-tmux-esenciales) — Sesiones, Ventanas, Paneles, Avanzado
- [Configuracion Recomendada](#configuracion-recomendada) — ~/.tmux.conf
- [Patrones de Uso en Auditoria](#patrones-de-uso-en-auditoria) — Recon paralelo, Escaneo, Testing, Documentacion
- [Integracion tmux + Claude Code](#integracion-tmux--claude-code)

## Por que tmux para Pentesting

- Multiples paneles visibles simultaneamente (recon + proxy + exploits)
- Sesiones persistentes: no se pierden si se cierra la terminal
- Flujo de trabajo organizado por ventanas/paneles
- Ideal para auditorias largas o remotas (via SSH)
- Integrable con scripts de automatizacion

---

## Sesion de Auditoria - Layout

### Creacion de sesion con layout predefinido

```bash
#!/bin/bash
# pentest-tmux.sh - Layout de auditoria web caja negra
# Uso: ./pentest-tmux.sh <target>

TARGET=${1:-"target.com"}
SESSION="audit-$(echo $TARGET | tr '.' '-')"

# Crear sesion con primera ventana: RECON
tmux new-session -d -s "$SESSION" -n "recon"
tmux send-keys -t "$SESSION:recon" "echo '[RECON] Target: $TARGET'" C-m

# Split horizontal: panel superior para comandos, inferior para resultados
tmux split-window -v -t "$SESSION:recon"
tmux send-keys -t "$SESSION:recon.1" "echo 'Resultados de recon'" C-m

# Ventana 2: ENUM (enumeracion y escaneo)
tmux new-window -t "$SESSION" -n "enum"
tmux send-keys -t "$SESSION:enum" "echo '[ENUM] Enumeracion de $TARGET'" C-m
tmux split-window -v -t "$SESSION:enum"

# Ventana 3: EXPLOIT (testing manual)
tmux new-window -t "$SESSION" -n "exploit"
tmux send-keys -t "$SESSION:exploit" "echo '[EXPLOIT] Testing de $TARGET'" C-m
tmux split-window -h -t "$SESSION:exploit"

# Ventana 4: TOOLS (herramientas auxiliares)
tmux new-window -t "$SESSION" -n "tools"
tmux send-keys -t "$SESSION:tools" "echo '[TOOLS] Herramientas auxiliares'" C-m
tmux split-window -h -t "$SESSION:tools"
tmux split-window -v -t "$SESSION:tools.1"

# Ventana 5: NOTES (documentacion en tiempo real)
tmux new-window -t "$SESSION" -n "notes"
tmux send-keys -t "$SESSION:notes" "echo '[NOTES] Notas de auditoria'" C-m

# Volver a la primera ventana
tmux select-window -t "$SESSION:recon"

# Attach a la sesion
tmux attach-session -t "$SESSION"
```

### Layout Visual

```
VENTANA: recon                    VENTANA: enum
+---------------------------+    +---------------------------+
| subfinder / amass         |    | nmap / feroxbuster        |
| theHarvester              |    | nuclei / nikto            |
+---------------------------+    +---------------------------+
| httpx / resultados        |    | resultados / analisis     |
+---------------------------+    +---------------------------+

VENTANA: exploit                  VENTANA: tools
+-------------+-------------+    +-------------+-------------+
| sqlmap      | xsstrike    |    | caido CLI   | curl / httpie|
| commix      | payloads    |    +-------------+-------------+
|             |             |    | hashcat     |
+-------------+-------------+    | john        |
                                 +-------------+

VENTANA: notes
+---------------------------+
| vim notas.md              |
| documentacion en vivo     |
+---------------------------+
```

---

## Comandos tmux Esenciales

### Gestion de Sesiones

```bash
# Crear sesion nueva
tmux new -s audit

# Listar sesiones activas
tmux ls

# Attach a sesion existente
tmux attach -t audit

# Detach de sesion (sin cerrarla)
# Prefix + d  (Ctrl+b, d)

# Renombrar sesion
# Prefix + $

# Matar sesion
tmux kill-session -t audit
```

### Gestion de Ventanas

```bash
# Crear ventana nueva
# Prefix + c

# Siguiente ventana
# Prefix + n

# Ventana anterior
# Prefix + p

# Ir a ventana por numero
# Prefix + 0-9

# Renombrar ventana
# Prefix + ,

# Cerrar ventana
# Prefix + &

# Listar ventanas
# Prefix + w
```

### Gestion de Paneles

```bash
# Split horizontal
# Prefix + "

# Split vertical
# Prefix + %

# Navegar entre paneles
# Prefix + flechas

# Redimensionar panel
# Prefix + Ctrl+flechas

# Zoom panel (toggle fullscreen)
# Prefix + z

# Cerrar panel
# Prefix + x

# Rotar paneles
# Prefix + o
```

### Funciones Avanzadas

```bash
# Modo scroll/copy
# Prefix + [
# (navegar con flechas, q para salir)

# Buscar en scroll buffer
# Prefix + [, luego /texto

# Enviar comando a panel especifico
tmux send-keys -t "audit:recon.0" "nmap -sC target.com" C-m

# Capturar output de panel a archivo
tmux capture-pane -t "audit:recon.0" -p > recon-output.txt

# Sincronizar input en todos los paneles
# Prefix + :setw synchronize-panes on
```

---

## Configuracion Recomendada

```bash
# ~/.tmux.conf - Config para pentesting

# Prefix mas comodo (Ctrl+a en vez de Ctrl+b)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Mouse habilitado (util para seleccionar paneles)
set -g mouse on

# Historial largo (para no perder output)
set -g history-limit 50000

# Numerar ventanas desde 1
set -g base-index 1
setw -g pane-base-index 1

# Renumerar ventanas al cerrar una
set -g renumber-windows on

# Status bar informativo
set -g status-style bg=black,fg=green
set -g status-left '[#S] '
set -g status-right '%H:%M | %d-%m-%Y'
set -g status-left-length 30

# Splits mas intuitivos
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"

# Navegacion entre paneles con Alt+flechas (sin prefix)
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Nuevas ventanas en el directorio actual
bind c new-window -c "#{pane_current_path}"

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded"

# Clipboard integration
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "xclip -selection clipboard"
```

---

## Patrones de Uso en Auditoria

### Patron: Recon en Paralelo

```bash
# En ventana recon, lanzar multiples herramientas simultaneamente:

# Panel 0: subdominios
subfinder -d target.com -o subs.txt && httpx -l subs.txt -o live.txt

# Panel 1: tecnologias y WAF
whatweb https://target.com && wafw00f https://target.com
```

### Patron: Escaneo Monitoreado

```bash
# Panel 0: ejecutar escaneo
nuclei -u https://target.com -severity critical,high -o nuclei-results.txt

# Panel 1: monitorear resultados en tiempo real
tail -f nuclei-results.txt
```

### Patron: Testing Interactivo

```bash
# Panel 0: sqlmap corriendo
sqlmap -u "https://target.com/page?id=1" --batch

# Panel 1: curl manual para verificar
curl -s "https://target.com/page?id=1'" | head -20
```

### Patron: Documentacion en Vivo

```bash
# Ventana notes: vim con notas en markdown
vim audit-notes.md

# Mientras en otras ventanas ejecutas herramientas,
# alternar a notes para documentar hallazgos al instante
```

---

## Integracion tmux + Claude Code

Para sesiones interactivas donde claude opera herramientas via terminal:

```bash
# Crear sesion dedicada para interaccion
tmux new -s pentest-ai

# Claude puede enviar comandos a paneles especificos:
tmux send-keys -t "pentest-ai:recon.0" "subfinder -d target.com" C-m

# Leer output de un panel:
tmux capture-pane -t "pentest-ai:recon.0" -p

# Verificar que un comando termino (check si shell esta idle):
tmux display-message -t "pentest-ai:recon.0" -p "#{pane_current_command}"
```

### Workflow Interactivo

```
1. Usuario crea sesion tmux: tmux new -s audit
2. Claude envia comandos a paneles via tmux send-keys
3. Claude lee resultados via tmux capture-pane
4. Claude analiza output y decide siguiente paso
5. Resultados se documentan en ventana de notas
6. Usuario puede ver todo en tiempo real desde cualquier ventana
```
