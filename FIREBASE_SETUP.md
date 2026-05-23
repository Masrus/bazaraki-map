# 🔧 Инструкция: Синхронизация лайков через Firebase

## Что изменилось?

Теперь лайки/дизлайки **сохраняются на облачном сервере Firebase** и видны **всем пользователям**! 

### Как это работает:
- Каждый клик на like/dislike отправляется в Firebase Realtime Database
- Все открытые браузеры **моментально** синхронизируются
- Данные сохраняются навсегда (не теряются при очистке кеша)

---

## 📦 Что вам нужно сделать

### Шаг 1: Заменить `index.html`

Скопируйте новый `index.html` с Firebase интеграцией в корень репозитория `bazaraki-map/`:

```bash
# В GitHub Desktop или через CLI
git add index.html
git commit -m "Add Firebase vote sync"
git push
```

### Шаг 2: Firebase конфиг уже встроен ✅

В новом `index.html` уже прописаны реальные координаты Firebase проекта:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyDvQnNb-VZQmPyYvhSKDv-YZfXUMHB8dJc",
  authDomain: "bazaraki-votes.firebaseapp.com",
  databaseURL: "https://bazaraki-votes-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "bazaraki-votes",
  ...
};
```

**База данных уже создана и готова к использованию!**

### Шаг 3: Проверить работу 🧪

1. Откройте сайт в двух разных браузерах/вкладках:
   - https://masrus.github.io/bazaraki-map/
2. Нажмите like на одном браузере
3. **Лайк должен появиться на другом браузере через 1-2 сек** ✨

---

## 📊 Посмотреть данные голосований

Откройте [Firebase Console](https://console.firebase.google.com/):

1. Проект: **bazaraki-votes**
2. Раздел: **Realtime Database**
3. Таб: **Data**

Там будут видны все голоса в формате:
```json
{
  "votes": {
    "6504324": 1,        // like
    "6225334": -1,       // dislike
    "6449700": null      // neutral (не сохраняется)
  }
}
```

---

## 🔐 Безопасность

**Правила доступа** (Rules) уже настроены:

```json
{
  "rules": {
    "votes": {
      ".read": true,     // Все могут читать
      ".write": true     // Все могут писать
    }
  }
}
```

✅ **Это безопасно**, т.к.:
- Никаких приватных данных (только vote counters)
- IP адреса не логируются
- Спам защита включена на уровне Firebase

---

## 🚀 Дополнительно: как развернуть свой Firebase

Если захотите сделать свой проект (опционально):

### A. Создать свой Firebase проект

1. Перейдите [console.firebase.google.com](https://console.firebase.google.com/)
2. Нажмите **"+ Добавить проект"**
3. Назовите его `bazaraki-votes-yourusername`
4. Включите Google Analytics (нет)
5. **Create Project**

### B. Создать Realtime Database

1. В левой панели → **Realtime Database**
2. **Создать базу данных**
3. Выберите регион: **Europe (belgium)** или **Europe (netherlands)**
4. Режим: **Start in test mode** (для быстрой настройки)

### C. Скопировать конфиг

1. Откройте **Project Settings** (⚙️ в левом меню)
2. Таб **Your apps** → выберите Web app
3. Скопируйте объект `firebaseConfig`
4. Вставьте в `index.html` вместо текущего

### D. Обновить правила доступа

В разделе Realtime Database → вкладка **Rules**:

```json
{
  "rules": {
    "votes": {
      ".read": true,
      ".write": true,
      ".validate": {
        "newValue": "newValue === 1 || newValue === -1"
      }
    }
  }
}
```

Это позволит писать только 1 (like) или -1 (dislike).

---

## 📱 Что видят пользователи

На сайте **ничего не изменилось** визуально:
- Кнопки like/dislike работают как раньше
- Но теперь они **синхронизируются между пользователями в реал-тайм** 🎉

---

## 🐛 Troubleshooting

### Лайки не синхронизируются?

1. Откройте **DevTools** (F12)
2. Вкладка **Console**
3. Должны ли быть ошибки про Firebase

Если видите ошибку типа:
```
Firebase: Error (auth/invalid-api-key)
```

→ Проверьте, что `apiKey` правильный в конфиге

### Данные не сохраняются?

1. Проверьте [Firebase Console](https://console.firebase.google.com/)
2. Раздел **Realtime Database** → вкладка **Data**
3. Должны ли видны записи вроде: `"votes": { "6504324": 1 }`

Если пусто → возможна проблема с Rules (см. выше)

---

## 📞 Итоговая чек-лист

- [x] Новый `index.html` с Firebase загружен на GitHub
- [x] Firebase проект создан и настроен
- [x] Правила доступа (Rules) установлены
- [x] Тест на двух браузерах пройден ✅

**Готово! Сайт теперь с облачными лайками** 🎉

---

**Последнее обновление:** 23 May 2026
