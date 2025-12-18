# 🎬 WHEP Players Collection - 18+ WebRTC Implementations

Полная коллекция рабочих примеров WHEP (WebRTC-HTTP egress Protocol) плееров с использованием различных библиотек, фреймворков и подходов.

## 📋 Таблица плееров

| # | Название | Описание | Технология | Файл |
|---|----------|---------|-----------|------|
| 1 | Web Component Simple | Самый простой - 5 строк кода | Web Components | `01-web-component-simple.html` |
| 2 | Native WebRTC | Базовая реализация WebRTC | Native WebRTC | `02-native-webrtc.html` |
| 3 | Web Component Advanced | Web Component с управлением и статистикой | Web Components | `03-web-component-advanced.html` |
| 4 | Video.js Basic | Популярный плеер Video.js | Video.js 7.x | `04-videojs-basic.html` |
| 5 | Video.js + HLS | Video.js с резервным HLS потоком | Video.js + HLS.js | `05-videojs-hls-fallback.html` |
| 6 | Flussonic Basic | Flussonic WebRTC плеер | Flussonic SDK | `06-flussonic-basic.html` |
| 7 | Flussonic Advanced | Мониторинг, статистика, аналитика | Flussonic + Analytics | `07-flussonic-advanced.html` |
| 8 | Hls.js Hybrid | Гибридный подход WebRTC + HLS | HLS.js + Native WebRTC | `08-hlsjs-webrtc-hybrid.html` |
| 9 | Dash.js | MPEG-DASH адаптивный плеер | Dash.js | `09-dashjs-adaptive.html` |
| 10 | Shaka Player | Google Shaka для DASH и HLS | Shaka Player (Google) | `10-shaka-player.html` |
| 11 | WebRTC Monitor | Детальный мониторинг PeerConnection | Native WebRTC + RTC Stats | `11-webrtc-monitor.html` |
| 12 | React Hooks | React функциональный компонент | React 18 + Hooks | `12-react-hooks.html` |
| 13 | Vue 3 Composition | Vue 3 с Composition API | Vue 3 | `13-vue3-composition.html` |
| 14 | Canvas Processor | Обработка видео через Canvas | Canvas API + Filters | `14-canvas-processor.html` |
| 15 | TypeScript Native | Native WebRTC с TypeScript типами | TypeScript + WebRTC | `15-typescript-native.html` |
| 16 | MediaRecorder | Запись видео стрима | MediaRecorder API | `16-mediarecorder.html` |
| 17 | Ultra Minimal | Одна строка кода | Native WebRTC (minified) | `17-ultra-minimal.html` |
| 18 | Audio Visualizer | Аудио визуализация | Tone.js + Web Audio API | `18-audio-visualizer.html` |

## 🚀 Быстрый старт

### Локальный запуск

```bash
# Клонируем репозиторий
git clone https://github.com/KonstantinSpivak/experiment-wehp.git
cd experiment-wehp

# Запускаем простой HTTP сервер
python3 -m http.server 8000

# Или используем Node.js
npx http-server
```

Затем откройте в браузере:
- **Главное меню**: `http://localhost:8000/index.html`
- **Прямой доступ к плеерам**: `http://localhost:8000/players/XX-name.html`

### WHEP Endpoint

```
http://192.168.0.108:8889/orange/stream/whep
```

Внесите ваш конкретный WHEP URL в каждый плеер (уже заполнен в примерах).

## 📦 Категории плееров

### Native WebRTC (2, 15, 17)
- Чистый JavaScript без зависимостей
- Максимальный контроль и гибкость
- Самый низкий overhead

### Web Components (1, 3)
- Переиспользуемые компоненты
- Инкапсуляция логики
- Простота интеграции

### Media Players (4, 5, 6, 8, 9, 10)
- Готовые решения для потокового видео
- Встроенные элементы управления
- Поддержка разных протоколов

### Frameworks (12, 13)
- React Hooks и состояние
- Vue 3 Composition API
- Компонентная архитектура

### Advanced (7, 11, 14, 16, 18)
- Мониторинг и аналитика
- Видео обработка (Canvas, filters)
- Запись потока (MediaRecorder)
- Аудио визуализация

## 🎯 Использование

### Вариант 1: Web Component (Самый быстрый)
```html
<whep-video src="http://192.168.0.108:8889/orange/stream/whep" autoplay muted controls></whep-video>
<script src="https://unpkg.com/@eyevinn/whep-video-component@latest/dist/whep-video.component.js"></script>
```

### Вариант 2: Native WebRTC (Полный контроль)
```javascript
const pc = new RTCPeerConnection({iceServers: [{urls: 'stun:stun.l.google.com:19302'}]});
pc.addTransceiver('video', {direction: 'recvonly'});
pc.ontrack = (e) => video.srcObject = e.streams[0];

const offer = await pc.createOffer();
await pc.setLocalDescription(offer);

const res = await fetch('http://192.168.0.108:8889/orange/stream/whep', {
    method: 'POST',
    headers: {'Content-Type': 'application/sdp'},
    body: offer.sdp
});

await pc.setRemoteDescription(new RTCSessionDescription({
    type: 'answer',
    sdp: await res.text()
}));
```

### Вариант 3: Video.js (Готовое решение)
```html
<video id="player" class="video-js" controls></video>
<script src="https://cdn.jsdelivr.net/npm/video.js@7/dist/video.min.js"></script>
<script>
    const player = videojs('player');
    player.src({src: 'http://192.168.0.108:8889/orange/stream/whep', type: 'application/x-whep'});
</script>
```

## 📊 Сравнение плееров

| Параметр | Native | Web Component | Video.js | Flussonic | React/Vue |
|----------|--------|---------------|----------|-----------|----------|
| **Минимум кода** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Зависимости** | 0 | 1 | 1+ | 1+ | 3+ |
| **Контроль** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| **Готовность** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Мониторинг** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ |

## 🛠️ Технологии

- **WebRTC**: Native Browser API
- **Web Components**: Custom Elements
- **Видео плееры**: Video.js, Flussonic, Dash.js, Shaka, HLS.js
- **Фреймворки**: React 18, Vue 3, TypeScript
- **Визуализация**: Canvas API, Tone.js, Web Audio API
- **Запись**: MediaRecorder API
- **Адаптивные потоки**: DASH, HLS, WebRTC

## 📈 Мониторинг и Статистика

Плееры 7, 11, 16 поддерживают:
- RTCPeerConnection статистика
- Битрейт и FPS мониторинг
- Задержку (latency) и потери пакетов
- Качество соединения
- Разрешение видео
- Аудио/видео кодеки

## ⚙️ Конфигурация

Все плееры настроены на WHEP URL:
```
http://192.168.0.108:8889/orange/stream/whep
```

Для изменения URL отредактируйте строку:
```javascript
const WHEP_URL = 'http://192.168.0.108:8889/orange/stream/whep';
```

или в HTML:
```html
src="http://192.168.0.108:8889/orange/stream/whep"
```

## 🔧 Требования

- Современный браузер с WebRTC поддержкой (Chrome, Firefox, Edge, Safari)
- HTTPS или localhost для WebRTC
- HTTP сервер для раздачи файлов
- Рабочий WHEP endpoint

## 📝 Лицензия

MIT

## 🤝 Вклад

Приветствуются pullRequests и issues!

## 📞 Контакты

- GitHub: [@KonstantinSpivak](https://github.com/KonstantinSpivak)
- Repository: [experiment-wehp](https://github.com/KonstantinSpivak/experiment-wehp)

---

**Created**: 2024
**Updated**: 2025-12-18
**Version**: 1.0 (18 плееров)
