### Инструкция по публикации статического сайта на GitHub Pages

---

## Способ 1. Deploy from a branch (встроенный, без GitHub Actions)

**Когда использовать:**  
Для простых статических сайтов (HTML, CSS, JS) без сборки.

**Шаги:**

1. **Подготовьте файлы**
    - Убедитесь, что в корне вашего репозитория есть файл `index.html`.
    - Все остальные файлы (стили, скрипты, картинки) должны быть в папках относительно корня.

2. **Запушьте изменения в удалённый репозиторий**
   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

3. **Включите GitHub Pages**
    - Перейдите на страницу вашего репозитория на GitHub.
    - Нажмите **Settings** → в левом меню выберите **Pages**.
    - В разделе **Build and deployment** → **Source** выберите **Deploy from a branch**.
    - В выпадающем списке **Branch** выберите `main`.
    - В выпадающем списке **Folder** выберите `/ (root)`.
    - Нажмите **Save**.

4. **Дождитесь публикации**
    - После сохранения настроек GitHub автоматически запустит процесс сборки и деплоя.
    - Перейдите на вкладку **Actions** – вы увидите запущенный workflow с названием **"pages build and deployment"**.
    - Дождитесь, пока рядом с ним загорится **зелёный кружок** (означает успешное завершение).
    - Затем обновите страницу **Settings → Pages** – вверху появится ссылка на ваш сайт.

5. **Проверьте сайт**
    - Откройте ссылку в режиме инкогнито (или сделайте жёсткую перезагрузку `Ctrl+F5`).

---

## Способ 2. GitHub Actions (автоматический деплой через workflow)

**Когда использовать:**  
Для проектов со сборкой (например, React, Vue) или если нужна гибкая настройка деплоя.

**Шаги:**

1. **Подготовьте файлы**
    - Убедитесь, что `index.html` лежит в корне репозитория (или настройте путь в workflow).

2. **Создайте workflow-файл**
    - В корне репозитория создайте папку `.github/workflows` (если её нет).
    - Внутри создайте файл `deploy.yml` со следующим содержимым:

   ```yaml
   name: Deploy to GitHub Pages

   on:
     push:
       branches: [ "main" ]
     workflow_dispatch:

   permissions:
     contents: read
     pages: write
     id-token: write

   jobs:
     deploy:
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       runs-on: ubuntu-latest
       steps:
         - name: Checkout
           uses: actions/checkout@v4
         - name: Setup Pages
           uses: actions/configure-pages@v4
         - name: Upload artifact
           uses: actions/upload-pages-artifact@v3
           with:
             path: '.'          # указывает корень репозитория
         - name: Deploy to GitHub Pages
           id: deployment
           uses: actions/deploy-pages@v4
   ```

   *Если ваш `index.html` лежит в подпапке (например, `public`), измените `path: '.'` на `path: 'public'`.*

3. **Запушьте изменения**
   ```bash
   git add .
   git commit -m "Add GitHub Pages workflow"
   git push origin main
   ```

4. **Настройте источник Pages**
    - Перейдите в **Settings → Pages**.
    - В разделе **Build and deployment** → **Source** выберите **GitHub Actions**.
    - Нажмите **Save** (если уже выбрано, ничего не меняйте).

5. **Запустите деплой**
    - Перейдите на вкладку **Actions** в вашем репозитории.
    - Вы увидите запущенный workflow с именем **Deploy to GitHub Pages**.
    - Дождитесь его успешного завершения (зелёная галочка).

6. **Найдите ссылку на сайт**
    - После успешного деплоя ссылка появится:
        - Вверху страницы **Settings → Pages**.
        - Или в логах workflow (шаг **Deploy to GitHub Pages**).
    - Откройте ссылку в режиме инкогнито.

---

### ❗ Возможные проблемы и их решение

| Проблема | Решение |
|----------|---------|
| **404 Not Found** | 1. Проверьте, что в настройках **Pages** выбран правильный источник (для способа 1 – `Deploy from a branch`, для способа 2 – `GitHub Actions`).<br>2. Убедитесь, что `index.html` находится в корне репозитория (или в папке, указанной в `path`).<br>3. Очистите кеш браузера или откройте сайт в режиме инкогнито. |
| **Файл не открывается локально (битый)** | Создайте новый файл `index.html` с минимальным содержимым (см. ниже) и запушьте заново. |
| **Workflow не запускается** | Проверьте, что файл `.github/workflows/deploy.yml` лежит в ветке `main` и назван правильно (без расширений). |
| **Сайт не обновляется** | Подождите 2–3 минуты (CDN кеширует). Сделайте жёсткую перезагрузку. |

**Минимальный рабочий `index.html`:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>My Site</title>
</head>
<body>
    <h1>Hello, world!</h1>
</body>
</html>
```

---

**Готово!** Ваш сайт опубликован.
