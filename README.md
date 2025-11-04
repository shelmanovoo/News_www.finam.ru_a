# News_www.finam.ru_a
Обработка новостей RSS с анализом и уведомлениями Этот workflow автоматизирует процесс обработки финансовых новостей из RSS-ленты Finam с использованием AI-анализа и отправкой уведомлений в Telegram.

<img width="1019" height="441" alt="image" src="https://github.com/user-attachments/assets/235a25c1-255d-43e6-bb37-cb76d9e9586c" />


Основные компоненты:

Триггер запуска (When Executed by Another Workflow)

Workflow запускается другим процессом с параметром store_news


Выборка новостей (Select rows from a table)

Извлекает из PostgreSQL таблицы finam_rss все записи с флагом new = 1 (новые необработанные новости)


AI-анализ (Basic LLM Chain + OpenRouter Chat Model)

Использует GPT-4.1-nano через OpenRouter для анализа текста новости
Извлекает ключевую информацию:

Персоны (PER): известные личности (например, Jerome Powell, Xi Jinping)
Организации (ORG): российские компании (Газпром, Сбербанк, Роснефть и др.)
Локации (LOC/GPE): страны и регионы
Тональность: позитивная/негативная/нейтральная
Категория: Компания/Политика/Финансы/Светская тема
Прогноз: краткосрочный прогноз по тикерам упомянутых компаний (рост/падение/без изменений на 1-3%)




Парсинг результата (Parser)

JavaScript-код обрабатывает JSON-ответ от AI
Структурирует данные для дальнейшего использования
Обрабатывает ошибки парсинга


Условная фильтрация (If1)

Отправляет уведомление только если:

Тональность НЕ нейтральная
Есть хотя бы один прогноз по тикерам




Отправка уведомления (Send Telegram Message)

Форматирует и отправляет сообщение в Telegram (chat_id: 1251086517)
Включает: дату, тему, персоны, страны, категорию, тональность, прогнозы и ссылку


Обновление статуса (Execute a SQL query)

Устанавливает флаг new = 0 для обработанной новости
Предотвращает повторную обработку



Логика работы:
Workflow обрабатывает финансовые новости, фильтрует значимые (с прогнозами и не нейтральной тональностью) и отправляет аналитические сводки в Telegram для быстрого принятия торговых решений.

Примеры обработки новостей:

stock, [3 Nov 2025 at 19:51:08]:
Дата:2025-10-29T13:45:46.000Z
Тема:Акции Nvidia резко растут на фоне позитива вокруг ИИ...
Персрны:
Страна:
Категория:Company
Тональность:positive
Прогноз:{
  "NVDA": "growth (+1-3%)"
}
link:https://www.finam.ru/publications/item/nvidia-stala-pervoy-kompaniey-v-istorii-s-kapitalizatsiey-v-5-trln-20251029-1700/?utmsource=rss&utmmedium=worldnews&newstofinamb=worldnews

This message was sent automatically with n8n


Дата:2025-10-29T09:43:35.000Z
Тема:Рыночный оптимизм подогрели заявления главы Nvidia Дженсена Хуана о ожиданиях заказов на ИИ-чипы на $500 млрд...
Персрны:Дженсен Хуан
Страна:
Категория:Company
Тональность:positive
Прогноз:{
  "NVDA": "growth (+1-3%)"
}
link:https://www.finam.ru/publications/item/aktsii-nvidia-vyrosli-na-3-priblizivshis-k-rekordnoy-otsenke-v-5-trln-20251029-1243/?utmsource=rss&utmmedium=worldnews&newstofinamb=worldnews

This message was sent automatically with n8n
