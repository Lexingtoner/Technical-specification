
# Задание 1: Анализ требований

## Найденные противоречия и недочёты

**Противоречие: пп. (подпункты) 1, 2 и 9**

В самом начале анализа требований в 1 пункт говорит, что минимальное количество товара - 1 единица. Пункт 9 допускает уменьшение количества до 0 с последующим удалением. Пункт 2 дополнительно усиливает путаницу, утверждая, что изменить количество можно «не менее, чем до 1». Итого три пункта противоречат друг другу: непонятно, возможен ли 0 как вводимое значение или это запрещено, а удаление происходит только кнопкой.

**Противоречие: пп. 7 и 13** 

Пункт 7 фиксирует цену на момент добавления в корзину. Пункт 13 требует автоматически обновлять цену в корзине при её изменении в каталоге. Это прямое взаимоисключение.

**Противоречие: пп. 1, 3 и 4 - потенциальная несогласованность лимитов**

5 товаров × 10 единиц (максимум по п. 1) = 50 штук, что в 2,5 раза превышает лимит в 20 штук (п. 4). Лимиты формально не противоречат, но совместно ограничивают поведение так, что пользователь не сможет добавить 5 разных товаров по 10 штук - нужно явно описать приоритет ограничений.

**Пропущенный пункт 12**

Нумерация переходит с 11 на 13. Пункт 12 отсутствует - опечатка или намеренный пропуск требует уточнения.

**Некорректный/неуместный пункт 11**
«Реклама должна быть каждый будний день по утрам и вечерам» - это не требование к функционалу корзины, а расписание маркетинговых активностей. Данное требование не поддаётся однозначной реализации: не указаны временны́е рамки «утра» и «вечера», канал доставки рекламы, не ясно, как это связано с отображением корзины. Данный пункт слишком размыт и неуместен.

**Пункт 5 - избыточен**
«Товары в корзине могут быть разные» полностью покрывается пунктом 3 (до 5 *различных* товаров). Пункт дублирует информацию и не несёт самостоятельной ценности.

**Пункт 6 - недостаточно конкретен**

Единое сообщение «Лимит корзины превышен» для всех ситуаций (превышение по количеству единиц одного товара, по числу позиций, по суммарному количеству) не информативно для пользователя.

**Пункт 8 - Некорректность**
В п. 8 не указана итоговая сумма всей корзины (Total), только стоимость по позициям.

---

## Исправленная версия ТЗ

**Раздел: Функционал корзины**

1. Пользователь может добавить в корзину от 1 до 10 единиц одного товара за одно действие.
2. Пользователь может изменить количество товара в корзине в диапазоне от 1 до 10. Уменьшение ниже 1 недоступно через поле ввода количества. Для удаления товара из корзины используется отдельная кнопка «Удалить».
3. В корзине может находиться не более 5 различных товарных позиций.
4. Суммарное количество всех единиц товара в корзине не может превышать 20 штук.
5. При попытке добавить товар, нарушающей любой из лимитов, система показывает соответствующее сообщение:
   - «Достигнут максимум по количеству этого товара (10 шт.)»
   - «В корзине максимальное число позиций (5 товаров)»
   - «Достигнут общий лимит корзины (20 шт.)»
6. Цена товара фиксируется на момент оформления заказа. В корзине всегда отображается актуальная цена из каталога. Если цена изменилась с момента последнего просмотра корзины, рядом с позицией отображается уведомление об изменении цены.
7. На странице корзины отображается список товаров с указанием: наименования, количества, актуальной цены за единицу и итоговой стоимости позиции. В нижней части - общая сумма заказа.
8. На странице корзины может отображаться блок с рекламой сопутствующих товаров. Конкретные условия показа рекламы определяются отдельным документом - маркетинговой спецификацией.

---

## Уточняющие вопросы к PM / бизнес-заказчику

**По ценообразованию:**
- Какое поведение ожидается, если цена товара снизилась - нужно ли уведомлять пользователя так же, как при повышении?
- Цена фиксируется при добавлении в корзину или при нажатии «Оформить заказ»? Это ключевое решение с UX и бизнес-последствиями.

**По лимитам:**
- Каков приоритет при одновременном достижении нескольких лимитов?
- Применяются ли лимиты одинаково для авторизованных и гостевых пользователей?
- Синхронизируется ли корзина между устройствами? Если да - как лимиты работают при параллельных сессиях?

**По рекламе:**
- Что конкретно подразумевается под «утрами» и «вечерами» - нужны точные временны́е диапазоны.
- Реклама отображается только авторизованным пользователям или всем?
- Кто управляет рекламным контентом - внутренняя команда или сторонняя система?

**По удалению товара:**
- Если пользователь удаляет товар - нужно ли запрашивать подтверждение?
- Нужна ли функциональность «отложенного» или «избранного» при удалении?

**По общей логике:**
- Что происходит с корзиной, если товар снят с продажи?
- Есть ли срок жизни корзины (например, товары удаляются через N дней)?
- Пункт 12 отсутствует в нумерации - это опечатка или было намеренно удалено требование?

---

# Задание 2: Проектирование API

**REST API запрос:**


```
GET /api/v1/partner-stores?user_lat=55.7558&user_lon=37.6173
Authorization: Bearer <token>
Accept: application/json
```

**Пример ответа:**

```json
{
  "status": "success",
  "data": {
    "stores": [
      {
        "id": "store_001",
        "name": "METRO",
        "logo_url": "https://cdn.petrushka.ru/logos/metro.png",
        "logo_background_color": "#B8D4F0",
        "external_url": "https://metro.ru/partner/petrushka",
        "delivery": {
          "type": "scheduled",
          "label": "Ближайшая доставка",
          "time_slot": "сегодня 21:00–23:00"
        },
        "is_available": true
      },
      {
        "id": "store_002",
        "name": "Ашан",
        "logo_url": "https://cdn.petrushka.ru/logos/auchan.png",
        "logo_background_color": "#1A1A1A",
        "external_url": "https://auchan.ru/partner/petrushka",
        "delivery": {
          "type": "scheduled",
          "label": "Ближайшая доставка",
          "time_slot": "сегодня 18:00–20:00"
        },
        "is_available": true
      },
      {
        "id": "store_003",
        "name": "ВкусВилл",
        "logo_url": "https://cdn.petrushka.ru/logos/vkusvill.png",
        "logo_background_color": "#2A2A2A",
        "external_url": "https://vkusvill.ru/partner/petrushka",
        "delivery": {
          "type": "express",
          "label": "Быстрая доставка",
          "time_slot": "от 20 до 60 минут",
          "highlight": true
        },
        "is_available": true
      },
      {
        "id": "store_004",
        "name": "ВИКТОРИЯ",
        "logo_url": "https://cdn.petrushka.ru/logos/victoria.png",
        "logo_background_color": "#4A6A1A",
        "external_url": "https://victoria.ru/partner/petrushka",
        "delivery": {
          "type": "scheduled",
          "label": "Ближайшая доставка",
          "time_slot": "сегодня 17:00–19:00"
        },
        "is_available": true
      }
    ]
  }
}
```

**Примечания по дизайну API:**
- `external_url` используется клиентом для открытия внешнего ресурса (WebView или браузер) при нажатии на карточку магазина.
- `highlight: true` в поле `delivery` сигнализирует клиенту, что текст нужно выделить цветом (как голубой у ВкусВилл на макете).
- `logo_background_color` позволяет серверу управлять цветом подложки логотипа без изменений на клиенте.
- Координаты пользователя (`user_lat`, `user_lon`) передаются для возможной будущей сортировки по близости магазина.

---

# Задание 3: Архитектура Push-уведомлений
**Описание архитектуры:**

Система работает в пять слоёв.

**Источники событий** - каждый микросервис публикует событие в брокер, когда что-то происходит: Order service сообщает об изменении статуса заказа или длительном бездействии корзины, Catalog service - об изменении цены, Promo service - о новой акции, Scheduler service запускает cron-задачи (например, «корзина брошена более 24 часов назад»).

**Message Broker (Kafka/RabbitMQ)** - все события попадают в отдельные топики. Это разделение гарантирует, что сбой рекламной рассылки никак не влияет на транзакционные уведомления о заказах.

**Notification service** - центральный сервис обрабатывает события из брокера. Внутри три ключевых компонента: Template engine подставляет данные в шаблоны текстов, Preference filter проверяет, хочет ли пользователь получать данный тип уведомлений, Dedup/rate limiter предотвращает спам и дублирование.

<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Push-уведомления — Петрушка Зеленая</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600;700&family=Unbounded:wght@400;700;900&display=swap');

  :root {
    --green: #2ECC71;
    --green-dark: #1a7a42;
    --green-light: #a8f0c8;
    --green-dim: #1b3d2a;
    --bg: #0d1a12;
    --bg2: #111f17;
    --bg3: #162a1e;
    --text: #e8f5ee;
    --muted: #5a8a6e;
    --accent: #f0c040;
    --red: #e05050;
    --blue: #50b0e8;
    --purple: #b080e8;
    --border: #2a4a35;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    min-height: 100vh;
    padding: 32px 20px 60px;
  }

  .page-header {
    text-align: center;
    margin-bottom: 48px;
  }

  .page-header h1 {
    font-family: 'Unbounded', sans-serif;
    font-size: clamp(18px, 4vw, 32px);
    font-weight: 900;
    color: var(--green);
    letter-spacing: -0.5px;
    line-height: 1.2;
  }

  .page-header .subtitle {
    font-size: 11px;
    color: var(--muted);
    margin-top: 8px;
    letter-spacing: 2px;
    text-transform: uppercase;
  }

  /* DIAGRAM WRAPPER */
  .diagram {
    max-width: 1100px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  /* ROW */
  .row {
    display: flex;
    align-items: stretch;
    gap: 0;
    position: relative;
  }

  /* SECTION LABEL */
  .layer-label {
    writing-mode: vertical-rl;
    transform: rotate(180deg);
    font-size: 9px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--muted);
    padding: 12px 8px;
    min-width: 28px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-right: 1px solid var(--border);
    background: var(--bg2);
  }

  .layer-content {
    flex: 1;
    padding: 20px 16px;
    background: var(--bg2);
    border-bottom: 1px solid var(--border);
  }

  .layer-title {
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 1.5px;
    text-transform: uppercase;
    margin-bottom: 14px;
  }

  /* NODES */
  .nodes {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    align-items: flex-start;
  }

  .node {
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 10px 14px;
    background: var(--bg3);
    font-size: 11px;
    line-height: 1.5;
    position: relative;
    transition: border-color 0.2s;
    min-width: 130px;
  }

  .node:hover { border-color: var(--green); }

  .node .node-title {
    font-weight: 700;
    font-size: 11px;
    margin-bottom: 3px;
  }

  .node .node-desc {
    font-size: 10px;
    color: var(--muted);
    line-height: 1.4;
  }

  /* COLOR VARIANTS */
  .node.green  { border-color: #2e6644; }
  .node.green .node-title { color: var(--green); }
  .node.yellow { border-color: #6a5520; }
  .node.yellow .node-title { color: var(--accent); }
  .node.blue   { border-color: #204a6a; }
  .node.blue .node-title   { color: var(--blue); }
  .node.purple { border-color: #4a2870; }
  .node.purple .node-title { color: var(--purple); }
  .node.red    { border-color: #6a2020; }
  .node.red .node-title    { color: var(--red); }

  /* ARROW between layers */
  .arrow-row {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 4px 0;
    position: relative;
  }

  .arrow-col {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
    flex: 1;
  }

  .arrow-line {
    width: 2px;
    height: 18px;
    background: var(--green-dark);
    position: relative;
  }

  .arrow-head {
    width: 0;
    height: 0;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 7px solid var(--green-dark);
  }

  .arrow-label {
    font-size: 9px;
    color: var(--muted);
    letter-spacing: 1px;
    text-align: center;
  }

  /* FLOW SECTION — main horizontal flow */
  .flow-section {
    margin: 40px 0 20px;
  }

  .flow-section h2 {
    font-family: 'Unbounded', sans-serif;
    font-size: 12px;
    color: var(--green);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 20px;
    padding-bottom: 8px;
    border-bottom: 1px solid var(--border);
  }

  /* HORIZONTAL FLOW */
  .hflow {
    display: flex;
    align-items: center;
    gap: 0;
    flex-wrap: wrap;
    row-gap: 16px;
  }

  .hflow-node {
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 10px 14px;
    background: var(--bg3);
    font-size: 10px;
    line-height: 1.5;
    min-width: 120px;
    flex-shrink: 0;
  }

  .hflow-node .nt { font-weight: 700; font-size: 11px; margin-bottom: 2px; }
  .hflow-node .nd { color: var(--muted); font-size: 9px; }

  .hflow-node.green .nt { color: var(--green); }
  .hflow-node.yellow .nt { color: var(--accent); }
  .hflow-node.blue .nt { color: var(--blue); }
  .hflow-node.purple .nt { color: var(--purple); }
  .hflow-node.red .nt { color: var(--red); }

  .harrow {
    display: flex;
    align-items: center;
    flex-direction: column;
    gap: 1px;
    padding: 0 6px;
    flex-shrink: 0;
  }

  .harrow-line {
    height: 2px;
    width: 24px;
    background: var(--green-dark);
  }

  .harrow-head {
    width: 0; height: 0;
    border-top: 5px solid transparent;
    border-bottom: 5px solid transparent;
    border-left: 7px solid var(--green-dark);
    margin-left: -1px;
  }

  .harrow-lbl {
    font-size: 8px;
    color: var(--muted);
    white-space: nowrap;
    margin-top: 3px;
  }

  /* TWO COLUMN LAYOUT */
  .two-col {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-top: 20px;
  }

  @media (max-width: 700px) {
    .two-col { grid-template-columns: 1fr; }
    .hflow { flex-direction: column; align-items: flex-start; }
    .harrow { flex-direction: row; transform: rotate(90deg); margin: 4px 0; }
  }

  .box {
    border: 1px solid var(--border);
    border-radius: 8px;
    background: var(--bg2);
    padding: 16px;
  }

  .box h3 {
    font-size: 10px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 12px;
  }

  .tag-list {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }

  .tag {
    font-size: 9px;
    padding: 3px 8px;
    border-radius: 3px;
    border: 1px solid;
    background: transparent;
  }

  .tag.g { border-color: #2e6644; color: var(--green-light); }
  .tag.y { border-color: #6a5520; color: var(--accent); }
  .tag.b { border-color: #204a6a; color: var(--blue); }
  .tag.p { border-color: #4a2870; color: var(--purple); }
  .tag.r { border-color: #6a2020; color: var(--red); }

  .step-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .step {
    display: flex;
    gap: 10px;
    align-items: flex-start;
    font-size: 10px;
    line-height: 1.5;
  }

  .step-num {
    min-width: 20px;
    height: 20px;
    border-radius: 50%;
    background: var(--green-dim);
    border: 1px solid var(--green-dark);
    color: var(--green);
    font-size: 9px;
    font-weight: 700;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    margin-top: 1px;
  }

  .step-text { color: var(--muted); }
  .step-text strong { color: var(--text); }

  /* LEGEND */
  .legend {
    display: flex;
    flex-wrap: wrap;
    gap: 14px;
    margin-top: 32px;
    padding-top: 16px;
    border-top: 1px solid var(--border);
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 9px;
    color: var(--muted);
  }

  .legend-dot {
    width: 8px; height: 8px;
    border-radius: 2px;
    flex-shrink: 0;
  }

  /* DIVIDER */
  .divider {
    height: 1px;
    background: var(--border);
    margin: 28px 0;
  }

  /* SECTION BADGE */
  .badge {
    display: inline-block;
    font-size: 9px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--green);
    background: var(--green-dim);
    border: 1px solid var(--green-dark);
    padding: 3px 10px;
    border-radius: 3px;
    margin-bottom: 14px;
  }

  /* ANIMATED PULSE */
  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .live { animation: pulse 2s ease-in-out infinite; }

</style>
</head>
<body>

<div class="page-header">
  <h1>🥬 Петрушка Зеленая</h1>
  <div class="subtitle">Архитектура Push-уведомлений · Мобильное приложение</div>
</div>

<div class="diagram">

  <!-- ========== BLOCK 1: MAIN FLOW ========== -->
  <div class="badge">01 — Верхнеуровневая схема</div>

  <div class="hflow">
    <div class="hflow-node green">
      <div class="nt">📱 Мобильное приложение</div>
      <div class="nd">iOS / Android<br>Регистрация device token</div>
    </div>
    <div class="harrow">
      <div style="display:flex;align-items:center;">
        <div class="harrow-line"></div>
        <div class="harrow-head"></div>
      </div>
      <div class="harrow-lbl">device token</div>
    </div>
    <div class="hflow-node yellow">
      <div class="nt">🔑 Token Service</div>
      <div class="nd">Хранит токены<br>устройств по user_id</div>
    </div>
    <div class="harrow">
      <div style="display:flex;align-items:center;">
        <div class="harrow-line"></div>
        <div class="harrow-head"></div>
      </div>
      <div class="harrow-lbl">token lookup</div>
    </div>
    <div class="hflow-node blue">
      <div class="nt">📤 Notification Service</div>
      <div class="nd">Бизнес-логика,<br>шаблоны, очередь</div>
    </div>
    <div class="harrow">
      <div style="display:flex;align-items:center;">
        <div class="harrow-line"></div>
        <div class="harrow-head"></div>
      </div>
      <div class="harrow-lbl">push payload</div>
    </div>
    <div class="hflow-node purple">
      <div class="nt">🌐 Push Gateway</div>
      <div class="nd">FCM (Android)<br>APNs (iOS)</div>
    </div>
    <div class="harrow">
      <div style="display:flex;align-items:center;">
        <div class="harrow-line"></div>
        <div class="harrow-head"></div>
      </div>
      <div class="harrow-lbl">push delivery</div>
    </div>
    <div class="hflow-node green">
      <div class="nt">📱 Устройство пользователя</div>
      <div class="nd">Получает уведомление<br>в трее / на экране</div>
    </div>
  </div>

  <div class="divider"></div>

  <!-- ========== BLOCK 2: TRIGGER SOURCES ========== -->
  <div class="badge">02 — Источники триггеров</div>

  <div class="nodes" style="margin-bottom: 12px;">
    <div class="node yellow">
      <div class="node-title">🛒 Order Service</div>
      <div class="node-desc">Заказ в корзине > N часов<br>Статус изменён / отменён<br>Доставка в пути / выполнена</div>
    </div>
    <div class="node yellow">
      <div class="node-title">📦 Warehouse Service</div>
      <div class="node-desc">Товар снова в наличии<br>Остаток &lt; порога (wishlist)</div>
    </div>
    <div class="node yellow">
      <div class="node-title">💳 Payment Service</div>
      <div class="node-desc">Оплата прошла / отклонена<br>Возврат средств</div>
    </div>
    <div class="node red">
      <div class="node-title">📣 Marketing Service</div>
      <div class="node-desc">Рекламные рассылки<br>Акции, скидки, купоны<br>Персонализированные офферы</div>
    </div>
    <div class="node purple">
      <div class="node-title">⏰ Scheduler</div>
      <div class="node-desc">Cron-триггеры<br>Отложенные уведомления<br>Напоминания по расписанию</div>
    </div>
    <div class="node blue">
      <div class="node-title">👤 User Service</div>
      <div class="node-desc">Welcome push<br>Повторная активация<br>Дни рождения / бонусы</div>
    </div>
  </div>

  <!-- Arrow down -->
  <div style="display:flex; align-items:center; justify-content:center; padding: 4px 0 0; flex-direction:column; gap:0;">
    <div style="width:2px;height:14px;background:var(--green-dark);"></div>
    <div style="font-size:9px;color:var(--muted);letter-spacing:1px;">Event / Command</div>
    <div style="width:2px;height:10px;background:var(--green-dark);"></div>
    <div style="width:0;height:0;border-left:5px solid transparent;border-right:5px solid transparent;border-top:7px solid var(--green-dark);"></div>
  </div>

  <!-- Message Broker -->
  <div style="display:flex;justify-content:center; margin: 6px 0;">
    <div class="node" style="border-color:#405a48; background:#141f19; min-width:280px; text-align:center;">
      <div class="node-title" style="color:#8de8b0; font-size:13px; margin-bottom:4px;">⚡ Message Broker</div>
      <div class="node-desc">Kafka / RabbitMQ<br>Топики: <span style="color:var(--accent)">push.order</span> · <span style="color:var(--blue)">push.marketing</span> · <span style="color:var(--purple)">push.system</span></div>
    </div>
  </div>

  <!-- Arrow down -->
  <div style="display:flex; align-items:center; justify-content:center; padding: 4px 0 0; flex-direction:column; gap:0;">
    <div style="width:2px;height:10px;background:var(--green-dark);"></div>
    <div style="width:0;height:0;border-left:5px solid transparent;border-right:5px solid transparent;border-top:7px solid var(--green-dark);"></div>
  </div>

  <div style="display:flex;justify-content:center; margin: 6px 0;">
    <div class="node blue" style="min-width:280px; text-align:center;">
      <div class="node-title" style="font-size:13px; margin-bottom:4px;">📤 Notification Service</div>
      <div class="node-desc">
        Обогащение данными · Шаблонизация<br>
        Тихие часы / Opt-out фильтр · Rate limiting<br>
        Приоритизация · A/B тесты
      </div>
    </div>
  </div>

  <div class="divider"></div>

  <!-- ========== BLOCK 3: DETAILED FLOW ========== -->
  <div class="badge">03 — Детальный поток обработки</div>

  <div class="step-list">
    <div class="step">
      <div class="step-num">1</div>
      <div class="step-text">
        <strong>Регистрация токена:</strong> Приложение при запуске запрашивает разрешение, получает device token от FCM/APNs и отправляет его в <strong>Token Service</strong> вместе с user_id, платформой и версией ОС.
      </div>
    </div>
    <div class="step">
      <div class="step-num">2</div>
      <div class="step-text">
        <strong>Генерация события:</strong> Микросервис (Order, Marketing и др.) публикует событие в брокер сообщений (Kafka/RabbitMQ) с типом, user_id и метаданными.
      </div>
    </div>
    <div class="step">
      <div class="step-num">3</div>
      <div class="step-text">
        <strong>Обработка в Notification Service:</strong> Консьюмер читает событие → проверяет opt-out пользователя → проверяет тихие часы (22:00–08:00) → выбирает шаблон → обогащает данными через другие сервисы.
      </div>
    </div>
    <div class="step">
      <div class="step-num">4</div>
      <div class="step-text">
        <strong>Отправка через Push Gateway:</strong> Notification Service запрашивает токен из Token Service → формирует payload → отправляет через <strong>FCM</strong> (Android) или <strong>APNs</strong> (iOS).
      </div>
    </div>
    <div class="step">
      <div class="step-num">5</div>
      <div class="step-text">
        <strong>Трекинг и аналитика:</strong> Каждая отправка логируется в <strong>Notification Log</strong>. Приложение отправляет callback о доставке и клике. Данные идут в <strong>Analytics Service</strong> (CTR, открытия).
      </div>
    </div>
    <div class="step">
      <div class="step-num">6</div>
      <div class="step-text">
        <strong>Обработка ошибок:</strong> При невалидном токене — удаление из Token Service. При временных ошибках — retry с exponential backoff. Dead-letter queue для неотправленных.
      </div>
    </div>
  </div>

  <div class="divider"></div>

  <!-- ========== BLOCK 4: Two columns ========== -->
  <div class="two-col">

    <div class="box">
      <h3>Типы Push-уведомлений</h3>
      <div class="tag-list">
        <span class="tag y">🛒 Брошенная корзина</span>
        <span class="tag y">❌ Отмена заказа</span>
        <span class="tag y">✅ Заказ подтверждён</span>
        <span class="tag y">🚚 Заказ в пути</span>
        <span class="tag y">📦 Доставлен</span>
        <span class="tag g">💰 Оплата прошла</span>
        <span class="tag r">⚠️ Ошибка оплаты</span>
        <span class="tag b">📣 Акция / скидка</span>
        <span class="tag b">🎁 Персональный оффер</span>
        <span class="tag p">🔔 Товар появился</span>
        <span class="tag g">🎂 День рождения</span>
        <span class="tag p">⏳ Срок бонусов истекает</span>
      </div>
    </div>

    <div class="box">
      <h3>Ключевые компоненты</h3>
      <div class="tag-list">
        <span class="tag g">Token Service</span>
        <span class="tag b">Notification Service</span>
        <span class="tag y">Message Broker (Kafka)</span>
        <span class="tag p">FCM / APNs Gateway</span>
        <span class="tag g">Template Engine</span>
        <span class="tag r">Opt-out / Preferences</span>
        <span class="tag b">Scheduler (Quartz/cron)</span>
        <span class="tag p">Notification Log DB</span>
        <span class="tag y">Analytics Service</span>
        <span class="tag r">Dead Letter Queue</span>
        <span class="tag g">Rate Limiter</span>
        <span class="tag b">A/B Test Engine</span>
      </div>
    </div>

  </div>

  <div class="divider"></div>

  <!-- ========== BLOCK 5: ADMIN ========== -->
  <div class="badge">04 — Административная панель</div>

  <div class="nodes">
    <div class="node red">
      <div class="node-title">🖥️ Admin Panel</div>
      <div class="node-desc">Создание рассылок<br>Сегментация аудитории<br>Планировщик отправки</div>
    </div>
    <div class="node yellow">
      <div class="node-title">🎯 Segmentation</div>
      <div class="node-desc">Фильтр по RFM<br>Геолокация, поведение<br>Интересы, история заказов</div>
    </div>
    <div class="node blue">
      <div class="node-title">🧪 A/B Testing</div>
      <div class="node-desc">Разные тексты / CTA<br>Время отправки<br>Измерение конверсии</div>
    </div>
    <div class="node purple">
      <div class="node-title">📊 Analytics Dashboard</div>
      <div class="node-desc">Delivered / Opened / Clicked<br>Конверсия в заказ<br>Отписки по типу</div>
    </div>
  </div>

  <!-- LEGEND -->
  <div class="legend">
    <div class="legend-item">
      <div class="legend-dot" style="background:#2ECC71;"></div>
      <span>Клиентские / инфра сервисы</span>
    </div>
    <div class="legend-item">
      <div class="legend-dot" style="background:#f0c040;"></div>
      <span>Бизнес-триггеры</span>
    </div>
    <div class="legend-item">
      <div class="legend-dot" style="background:#50b0e8;"></div>
      <span>Notification слой</span>
    </div>
    <div class="legend-item">
      <div class="legend-dot" style="background:#b080e8;"></div>
      <span>Внешние платформы (FCM/APNs)</span>
    </div>
    <div class="legend-item">
      <div class="legend-dot" style="background:#e05050;"></div>
      <span>Маркетинг / Admin</span>
    </div>
  </div>

</div><!-- /diagram -->

</body>
</html>


**Push Gateway** - прослойка, которая знает, на какую платформу отправлять (FCM для Android, APNs для iOS), управляет токенами и обрабатывает ошибки доставки (невалидный токен → удаляем из базы).

**Регистрация токена** - при первом запуске приложения мобильный клиент получает device token от FCM/APNs и отправляет его в Notification service, где он сохраняется в базе данных привязанным к user_id. 

**Delivery receipt** - провайдеры возвращают статус доставки, что позволяет вести аналитику и повторные попытки.

