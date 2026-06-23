# 🖥️ VoidRP WebGUI

> Форк WebGUI v1.3.0 — встроенный Chromium-браузер в Minecraft-клиенте с двунаправленными событиями и VoidRP-кастомизациями.

![Minecraft](https://img.shields.io/badge/Minecraft-1.21.1-brightgreen?logo=minecraft)
![Fabric](https://img.shields.io/badge/Fabric-Stonecutter-3C4553)
![MCEF](https://img.shields.io/badge/MCEF-keksuccino_2.2.0-blue)
![Version](https://img.shields.io/badge/upstream-v1.3.0-green)
![License](https://img.shields.io/badge/license-proprietary-red)

---

## 🗺️ Место в экосистеме

```
  voidrp-webgui (клиентский мод, Fabric jar через Connector)
        │ получает S2C пакеты по каналам webgui:open_web / webgui:set_main_menu
        ▼
  Встроенный Chromium (MCEF keksuccino 2.2.0)
        │ открывает void-rp.ru/game-ui/*
        ▼
  minecraft-backend (FastAPI) ← webgui_token HMAC-SHA256
```

**Этот репо содержит исходники** для Fabric-версии мода. На сервере и в лаунчер-паке используется нативный NeoForge jar из [voidrp-webgui-neoforge](https://github.com/VOIDRP-MINECRAFT/voidrp-webgui-neoforge) — он работает через Sinytra Connector.

---

## ✨ Возможности

### Встроенный браузер
- Chromium через MCEF прямо в Minecraft клиенте
- **Fullscreen режим** — полноэкранный интерфейс (игровой рынок, казна, профиль)
- **HUD overlay** — прозрачное наложение поверх gameplay
- **Main Menu (F6)** — кастомное серверное меню

### Двунаправленные события (upstream v1.2.0+)

**Сервер → страница:**
```java
WebviewApi.emitToPage(player, "market:updated", "{\"balance\":12500}");
```
```js
window.addEventListener("webgui:market:updated", e => updateBalance(e.detail));
```

**Страница → сервер:**
```js
window.webgui.postToServer("buy_clicked", JSON.stringify({ itemId: "iron" }));
```

### Entity binding (upstream v1.3.0)
- Правый клик по entity → открывает привязанный URL
- Плейсхолдеры: `{entityId}`, `{entityType}`, `{playerName}`, `{playerUuid}`
- JS: `window.webgui.entity` — контекст сущности

---

## 🔧 VoidRP-кастомизации

### Кастомные JS-каналы (page→game)

Добавлены в `WebviewPageToClientBridge.java`. Страница управляет игрой через `window.cefQuery`:

```js
import { postToGame } from '@/composables/useWebGui.js'

// Выполнить команду от имени игрока
postToGame({ channel: "run_command", command: "/pm pickup" })

// Открыть другой GUI без round-trip к серверу
postToGame({ channel: "open_gui", url: "https://void-rp.ru/game-ui#market" })

// Открыть/обновить HUD
postToGame({ channel: "open_hud", url: "https://void-rp.ru/game-ui/hud" })
```

Ключ `"command"` используется вместо `"text"` — совместим с обоими вариантами.

### MCEF зеркало

Chromium CEF (~119 МБ) раздаётся с нашего сервера вместо GitHub:

```properties
# config/mcef.properties (в лаунчер-паке)
download-mirror=https://void-rp.ru/launcher/mcef
```

- Commit: `eaeb3d4370aa3526ee237ad1981ad59af3de4dd1`
- SHA256 файл: голый lowercase hex (не PowerShell-формат с заголовками)

### `"environment": "*"` — обязательное правило

`fabric.mod.json` всегда должен содержать `"environment": "*"`. Значение `"client"` говорит Connector не грузить мод на сервере → NeoForge mod-list negotiation видит мод только у клиента → все игроки дисконнектятся.

---

## 🚀 Сборка

```bash
cd voidrp_webgui

# Для NeoForge 1.21.1 (основной вариант через Connector)
./gradlew build -P stonecutter.version=1.21.1
# → versions/1.21.1/build/libs/webgui-1.3.0+mc1.21.1.jar

# Задеплоить на сервер и в лаунчер-пак
cp versions/1.21.1/build/libs/webgui-1.3.0+mc1.21.1.jar \
   /home/mironoouv/launcher/pack/mods/webgui-1.3.0+mc1.21.1.jar
python3 scripts/generate_launcher_manifest.py
```

**Примечание:** на сервере лучше использовать нативный NeoForge jar (`voidrp_webgui_neoforge`), а этот репо — для исходников и кастомизаций Fabric-стороны.

---

## 🔑 Upstream changelog (что добавил апстрим)

### v1.2.0 — двунаправленные события
- `WebviewServerEvents.java` — сервер регистрирует handler-ы на события от страницы
- `WebviewClientEmit.java` — S2C диспатч событий в JS
- Новые payload-ы: `WebviewEmitS2CPayload` + `WebviewPageToServerPayload`

### v1.3.0 — entity binding
- `EntityBindingStore.java` — UUID→{url, cancelInteraction} в `config/webgui/entity_bindings.json`
- `EntityInteractionListener.java` — правый клик по entity → открывает URL
- Команды: `/webgui bind entity <selector> <url> [cancelInteraction]`

---

## 🔗 Связанные репозитории

| Репо | Связь |
|---|---|
| [voidrp-webgui-neoforge](https://github.com/VOIDRP-MINECRAFT/voidrp-webgui-neoforge) | Нативный NeoForge серверный мод |
| [voidrp-site](https://github.com/VOIDRP-MINECRAFT/voidrp-site) | Страницы `/game-ui/*` в Vue 3 |
| [minecraft-backend](https://github.com/VOIDRP-MINECRAFT/minecraft-backend) | API с `webgui_token` авторизацией |
| [voidrp-gamesync-plugin](https://github.com/VOIDRP-MINECRAFT/voidrp-gamesync-plugin) | `WebGuiBridgeService` — отправка URL клиентам |

---

<div align="center">
<a href="https://void-rp.ru">🌐 Сайт</a> ·
<a href="https://github.com/VOIDRP-MINECRAFT">🏠 Организация</a> ·
<a href="https://github.com/VOIDRP-MINECRAFT/.github/blob/main/docs/WEBGUI_ARCHITECTURE.md">📐 WebGUI Architecture</a>
</div>
