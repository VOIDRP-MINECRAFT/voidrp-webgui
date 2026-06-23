# 🖥️ VoidRP WebGUI

> Форк WebGUI v1.3.0 — встроенный Chromium-браузер в Minecraft-клиенте с двунаправленными событиями.

![Minecraft](https://img.shields.io/badge/Minecraft-1.21.1-brightgreen?logo=minecraft)
![NeoForge](https://img.shields.io/badge/NeoForge-21.1.x-orange)
![Fabric](https://img.shields.io/badge/Fabric-1.21.x-3C4553)
![MCEF](https://img.shields.io/badge/MCEF-Chromium_embedded-blue)
![License](https://img.shields.io/badge/license-proprietary-red)

---

## 🗺️ Место в экосистеме

```
  Сервер (voidrp-webgui-neoforge) → команда /webgui open <url>
        │ S2C пакет с URL
        ▼
  Minecraft Client + voidrp-webgui
        │ встроенный Chromium (MCEF)
        ▼
  Открывает void-rp.ru (Vue 3 SPA) прямо в игре
        │ двунаправленные события page ↔ game
        ▼
  minecraft-backend (API)
```

---

## ✨ Возможности

### Встроенный браузер
- Chromium через **MCEF** прямо в Minecraft клиенте
- **Fullscreen режим** — полноэкранный интерфейс (инвентарь, магазин, профиль)
- **HUD overlay** — прозрачное наложение поверх игры

### Двунаправленные события (v1.2.0+)

**Сервер → страница:**
```java
WebviewApi.emitToPage(player, "market:order_filled", "{\"item\":\"iron\",\"amount\":64}");
```
```js
window.addEventListener("webgui:market:order_filled", e => {
    showNotification(e.detail);
});
```

**Страница → сервер:**
```js
window.webgui.postToServer("shop:buy_clicked", JSON.stringify({ itemId: "iron_sword" }));
```

### VoidRP расширения (v1.3.0)
- Серверный контроль URL — сервер указывает какую страницу открыть
- История навигации управляется сервером
- Кастомные JS-события для in-game UI элементов

---

## 📋 Требования

| Компонент | Версия |
|---|---|
| Minecraft | 1.21.1 |
| NeoForge **или** Fabric | 21.1.x / 0.16.x |
| MCEF | совместимая версия |
| Java | 21 |

**Устанавливается только на клиент.** Серверная часть — [voidrp-webgui-neoforge](https://github.com/VOIDRP-MINECRAFT/voidrp-webgui-neoforge).

---

## 🚀 Сборка

```bash
cd voidrp_webgui

# NeoForge 1.21.1
cd versions/1.21.1-neoforge
./gradlew build

# Fabric 1.21.1
cd versions/1.21.11-fabric
./gradlew build
```

---

## 🔗 Связанные репозитории

| Репо | Связь |
|---|---|
| [voidrp-webgui-neoforge](https://github.com/VOIDRP-MINECRAFT/voidrp-webgui-neoforge) | Серверная часть — отправляет URL клиентам |
| [voidrp-site](https://github.com/VOIDRP-MINECRAFT/voidrp-site) | Сайт, который открывается внутри браузера |
| [minecraft-backend](https://github.com/VOIDRP-MINECRAFT/minecraft-backend) | API для страниц внутри WebGUI |

---

<div align="center">
<a href="https://void-rp.ru">🌐 Сайт</a> ·
<a href="https://github.com/VOIDRP-MINECRAFT">🏠 Организация</a>
</div>
