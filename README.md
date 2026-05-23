# Bazaraki Favorites Map

Интерактивная карта избранных объявлений с Bazaraki.com с Material Design 3 интерфейсом, поддержкой like/dislike и адаптивностью на мобильных устройствах.

**Живой сайт:** https://masrus.github.io/bazaraki-map/

## 📋 Описание проекта

Проект собирает все избранные объявления с аккаунта Bazaraki (https://www.bazaraki.com/profile/favorites/) и выводит их на интерактивной карте с возможностью:

- 🗺️ Просмотра всех объявлений на карте OpenStreetMap
- 🔍 Поиска по районам, типу, цене
- 👍 Лайков/дизлайков (сохраняются в LocalStorage браузера)
- 📱 Полной адаптивности (desktop sidebar + mobile bottom sheet)
- 🎨 Material Design 3 дизайна

## 🏗️ Структура

```
bazaraki-map/
├── index.html          # Основной HTML файл (запускается на GitHub Pages)
└── README.md          # Этот файл
```

## 🔧 Как работает

### Сбор данных

Все 22 объявления хранятся в `const D = [...]` массиве в HTML файле с полной информацией:
- ID объявления
- Название (тип квартиры)
- Цена
- Локация/район
- **Реальные координаты** (вытащены из Bazaraki API: `/api/v2/geo/static/streets/{lng}/{lat}/`)
- URL на Bazaraki
- Фото (CDN ссылка)

### Координаты

Все координаты **проверены** через обратное геокодирование (Nominatim/OpenStreetMap):
- 11/22 совпадают идеально
- 11/22 точны с точностью до района (различия из-за того, что Nominatim не знает микрорайонов типа "Agia Triada")

**Пример проверки:**
```
✅ 6449700: Neapolis → Neapolis, Kefallinias, Λεμεσός
✅ 6286583: Agios Tychon → Leoforos Amathountos, Agios Tychon
✅ 5774686: Neapolis → Neapolis, Favierou, Λεμεσός
```

Все координаты взяты напрямую из Bazaraki API и соответствуют точкам на карте на сайте Bazaraki.

### Vote система

- Like/dislike кнопки на каждой карточке (desktop и mobile)
- Сохранение в `localStorage` браузера ключ: `bazaraki_votes`
- Формат: `{"6504324": 1, "6225334": -1, ...}` (1 = like, -1 = dislike, не сохраняется = neutral)
- Данные не отправляются на сервер, только локальное хранилище

### Карта и тайлы

- **Leaflet.js** — библиотека для карт
- **OpenStreetMap** — бесплатные, легальные тайлы (стабильнее Google)
- Маркеры с номерами (1-22)
- Красный маркер для объявления на продажу (€430,000)
- Попапы с фото, ценой, локацией и ссылкой на Bazaraki

### Дизайн — Material Design 3

**Desktop:**
- Боковая панель (400px) с поиском и списком карточек
- M3 filled cards с hover эффектами
- Icon buttons для like/dislike

**Mobile:**
- Bottom Sheet (свайпаемая снизу панель)
- Три состояния: collapsed (видна ручка), peek (полсписка), full (весь список)
- Свайп вверх-вниз для навигации
- Тап на карту — сворачивает лист

**Цветовая система M3:**
- Primary: `#1a6b52` (зелёный Кипра)
- Error: `#ba1a1a` (красный для продажи и лайков)
- Surface: `#fbfdf9` (почти белый фон)
- Typography: Roboto

## 📊 Данные — 22 объявления

| ID | Тип | Цена | Район | Статус |
|--|--|--|--|--|
| 6504324 | 3-bed | €3,600 | Agia Triada | ✅ |
| 6225334 | 3-bed | €5,000 | Potamos Germasogeias | ✅ |
| 6449700 | 3-bed | €2,450 | Neapolis | ✅ |
| 6482947 | 3-bed | €3,500 | Neapolis | ✅ |
| 6430919 | 3-bed | €1,750 | Neapolis | ✅ |
| 6510036 | 4-bed | €2,000 | Germasogeia Tourist | ✅ |
| 6472767 | 3-bed | €2,500 | Neapolis | ✅ |
| 6510005 | 3-bed | €1,850 | Neapolis | ✅ |
| 6467931 | 3-bed penthouse | €4,000 | Agia Triada | ✅ |
| 6306175 | 3-bed | €1,800 | Agia Triada | ✅ |
| 6122924 | 3-bed | €2,800 | Agia Triada | ✅ |
| 6399139 | 3-bed | €2,100 | Agia Triada | ✅ |
| 6511073 | 3-bed | €2,500 | Agia Triada | ✅ |
| 6173440 | 3-bed | €5,000 | Potamos Germasogeias | ✅ |
| 6005402 | 3-bed | €6,000 | Neapolis | ✅ |
| 6456157 | 3-bed | €2,500 | Neapolis | ✅ |
| 6201580 | 3-bed | €3,300 | Historical Center | ✅ |
| 6286583 | 3-bed | €2,500 | Agios Tychon | ✅ |
| 6286477 | 3-bed | €3,000 | Limassol Marina | ✅ |
| 5774686 | 3-bed | €430,000 | Neapolis | 🔴 FOR SALE |
| 6239433 | 3-bed | €3,800 | Mouttagiaka | ✅ |
| 6256108 | 4-bed | €4,000 | Germasogeia | ✅ |

## 🚀 Как добавить/изменить объявления

В `index.html` найди массив `const D = [...]` и добавь/измени объекты в формате:

```javascript
{
  i: '6504324',              // ID объявления
  t: '3-bed apartment',      // Тип
  p: '€3,600',               // Цена (со знаком €)
  l: 'Agia Triada',          // Локация/район
  lat: 34.6794,              // Широта
  lng: 33.05202,             // Долгота
  u: 'https://www.bazaraki.com/adv/6504324...',  // URL
  img: 'https://cdn1.bazaraki.com/...',          // Фото CDN
  sale: false                // true если на продажу
}
```

### Как получить координаты нового объявления

1. Зайти на страницу объявления на Bazaraki
2. Нажать на карту (карта подгружается)
3. В dev tools → Network → найти запрос `/api/v2/geo/static/streets/...`
4. Скопировать координаты: `/api/v2/geo/static/streets/{lng}/{lat}/...`

Или через обратное геокодирование:
```
https://nominatim.openstreetmap.org/reverse?format=json&lat=34.6794&lon=33.05202
```

## 📦 Как развернуть на своём сервере

Скачать `index.html` и открыть локально в браузере, или залить на любой хостинг (Netlify, Vercel, GitHub Pages и т.д.).

**Требования:**
- Интернет (для OpenStreetMap тайлов и CDN картинок)
- Современный браузер (Chrome, Firefox, Safari, Edge)
- Ничего не нужно устанавливать

## 🔐 Безопасность

- Данные сохраняются **только в LocalStorage браузера** (не на сервере)
- Нет серверной части, всё работает на клиенте
- Фото загружаются с Bazaraki CDN (никак не хранятся)
- GitHub Pages хостит только статический HTML

## 🐛 Известные ограничения

1. Поиск работает с точным совпадением районов/цены (реализовано `indexOf`)
2. Фото могут быть недоступны если Bazaraki удалит объявление
3. При добавлении новых объявлений нужно вручную редактировать HTML (нет админ-панели)
4. Like/dislike данные теряются если очистить кеш браузера

## 🔮 Возможные улучшения

- [ ] API для автоматической синхронизации с Bazaraki
- [ ] Экспорт избранных в CSV/JSON
- [ ] Фильтры по цене, районам, типам
- [ ] История изменения цен
- [ ] Комментарии к объявлениям
- [ ] Синхронизация лайков между устройствами (требует backend)
- [ ] Тёмная тема
- [ ] Многоязычная поддержка

## 📞 Контакты

**Проект:** Bazaraki Favorites Map
**Создатель:** Alexander Mamyrin (Masrus)
**GitHub:** https://github.com/Masrus/bazaraki-map
**Email:** a.mamyrin@gmail.com

## 📄 Лицензия

MIT License — используй как хочешь, с указанием авторства.

## 🙏 Спасибо

- [Leaflet.js](https://leafletjs.com/) — карты
- [OpenStreetMap](https://openstreetmap.org/) — данные карт
- [Material Design 3](https://m3.material.io/) — дизайн система
- [Google Fonts](https://fonts.google.com/) — Roboto и Material Symbols
- [Bazaraki.com](https://www.bazaraki.com/) — данные объявлений

---

**Последнее обновление:** 23 May 2026
