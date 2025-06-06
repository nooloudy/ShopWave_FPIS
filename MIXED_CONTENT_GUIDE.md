# Устранение проблем Mixed Content в ShopWave

Этот документ содержит инструкции по устранению проблем "mixed content" (смешанного контента) при работе с HTTPS фронтендом и HTTP бэкендом.

## Что такое Mixed Content?

"Mixed content" возникает, когда начальная HTML-страница загружается по защищенному HTTPS-соединению, но запрашивает подресурсы (изображения, скрипты, API и т.д.) через незащищенное HTTP-соединение.

Современные браузеры блокируют такие запросы в целях безопасности, что приводит к ошибкам и нарушению работы приложения.

## Как мы исправили проблемы Mixed Content

1. **Относительные пути для API**:

   - Используем относительные пути `/api/...` вместо абсолютных `http://...`
   - Настроили rewrites в Next.js для перенаправления запросов к бэкенду

2. **HTTPS для бэкенда**:

   - Перешли на HTTPS для бэкенда: `https://131.189.96.66/api`
   - Даже с самоподписанным сертификатом это решает проблему смешанного контента

3. **Обработка медиа-файлов**:
   - Добавили rewrites для `/api/media` к бэкенду
   - Обновили все пути к изображениям на `/api/media/...`

## Проверка на наличие проблем Mixed Content

1. **Запустите тестовый сервер через HTTPS**:

   ```powershell
   # Сначала сгенерируйте сертификаты
   ./generate-certs.ps1

   # Затем запустите тест
   ./test-mixed-content.ps1
   ```

2. **Используйте встроенный детектор**:
   В консоли браузера выполните:
   ```javascript
   checkMixedContent();
   ```

## Если проблемы остаются

1. **Проверьте консоль браузера** для выявления URL, вызывающих проблемы

2. **Обновите .env.local**:

   ```
   NEXT_PUBLIC_API_URL=https://131.189.96.66/api
   ```

3. **Перезапустите сервер разработки**:

   ```
   npm run dev
   ```

4. **Проверьте файл конфигурации**:
   Убедитесь, что в `next.config.mjs` настроены правила rewrites с использованием HTTPS

5. **Очистите кэш браузера** и проверьте снова

## Справка по инструментам

- **generate-certs.ps1**: Сгенерировать самоподписанные SSL сертификаты
- **test-mixed-content.ps1**: Протестировать сайт на HTTPS с проверкой mixed content
- **lib/config/env.ts**: Центральная конфигурация API URL
- **mixed-content-detector.js**: Встроенный детектор проблем
