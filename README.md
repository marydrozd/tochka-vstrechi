# Telegram-бот «Точка встречи» — серверная часть (v34, пробная)

Бот: @tochka_vstrechi_app (t.me/tochka_vstrechi_app).

Ничего из этого ещё не залито в живой Supabase и на GitHub.

- `supabase-v34-telegram.sql` — таблицы и функции: user_places («Мои адреса» в аккаунте), telegram_links, коды привязки,
  функции бота (bot_*), триггер на walk_invites → бот, напоминания раз в 10 минут (pg_cron). Нужны v31 и v32 SQL.
  Проверено на локальном PostgreSQL 16: повторный запуск ок, привязка по коду (код одноразовый), ответ «Иду» от имени
  пользователя, вызов бота при новом приглашении и ответе, напоминание один раз, чужие таблицы и bot_* недоступны обычному пользователю.
- `functions/telegram-bot/index.ts` — бот: команды /start /walk /invites /places /settings /help, кнопки Иду/Не могу,
  сообщения о приглашениях и ответах, напоминания, тихий режим 23:00–9:00, разовая настройка (вебхук, меню, описание).
- `functions/telegram-login/index.ts` — вход в мини-приложение без пароля по подписи Telegram (initData) и привязка после входа ником и паролем.
- `ekrany-telegram.png/.html` — макет экранов.

Порядок включения (после «да»):
1. Supabase → Database → Extensions: включить pg_net и pg_cron. Запустить supabase-v34-telegram.sql.
2. Secrets функций: TELEGRAM_BOT_TOKEN (вставляет владелец), TG_WEBHOOK_SECRET, TG_HOOK_SECRET, APP_URL, ALLOWED_ORIGIN.
3. Vault: tg_hook_url = https://<проект>.supabase.co/functions/v1/telegram-bot, tg_hook_secret = как TG_HOOK_SECRET.
4. Задеплоить обе функции с --no-verify-jwt.
5. POST {"kind":"setup"} на telegram-bot с заголовком x-tochka-hook — бот сам поставит вебхук, меню и описание.
6. Сайт v34: кнопка «Подключить Telegram», синхронизация адресов, режим ?tg=1 (делается поверх v33).
