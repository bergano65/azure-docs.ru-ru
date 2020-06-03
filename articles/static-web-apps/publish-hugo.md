---
title: Руководство по Публикация сайта Hugo в службе "Статические веб-приложения Azure"
description: Узнайте, как развернуть приложение Hugo в службе "Статические веб-приложения Azure".
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 6debf422d0c16a6a2bfe180e6febb4973846e0f0
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870700"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Руководство по Публикация сайта Hugo в предварительной версии службы "Статические веб-приложения Azure"

В этой статье показано, как создать и развернуть веб-приложение [Hugo](https://gohugo.io/) в службе [Статические веб-приложения Azure](overview.md). Конечным результатом будет новый ресурс Статических веб-приложений Azure (со связанными действиями GitHub), позволяющий управлять процессом сборки и публикации приложения.

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> - Создание приложения Hugo
> - Настройка Статических веб-приложений Azure
> - Развертывание приложения Hugo в Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. Если ее нет, можно создать [учетную запись бесплатно](https://azure.microsoft.com/free/).
- Учетная запись GitHub. Если ее нет, можно создать [учетную запись бесплатно](https://github.com/join).

## <a name="create-a-hugo-app"></a>Создание приложения Hugo

Создайте приложение Hugo с помощью интерфейса командной строки (CLI) Hugo.

1. Выполните инструкции из [руководства по установке](https://gohugo.io/getting-started/installing/) Hugo в вашей операционной системе.

1. Откройте окно терминала.

1. Запустите интерфейс командной строки Hugo, чтобы создать новое приложение.

   ```bash
   hugo new site static-app
   ```

1. Перейдите к только что созданному приложению.

   ```bash
   cd static-app
   ```

1. Инициализируйте репозиторий Git.

   ```bash
    git init
   ```

1. Затем добавьте тему на сайт, установив ее в качестве подмодуля Git и указав ее в файле конфигурации Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Зафиксируйте изменения.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Отправьте приложение в GitHub.

Для подключения к Статическим веб-приложениям Azure требуется репозиторий на сайте GitHub. Ниже показано, как создать репозиторий для своего сайта.

1. Создайте пустой репозиторий GitHub (не создавая файл сведений) со страницы [https://github.com/new](https://github.com/new) с именем **hugo-static-app**.

1. Добавьте репозиторий GitHub в локальный репозиторий в качестве удаленного. В следующей команде не забудьте добавить имя пользователя GitHub вместо заполнителя `<YOUR_USER_NAME>`.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Отправьте локальный репозиторий на сайт GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Развертывание веб-приложения

Ниже описано, как создать новое статическое приложение сайта и развернуть его в рабочей среде.

### <a name="create-the-application"></a>Создание приложения

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Щелкните **Создать ресурс**.
1. Выполните поиск по запросу **Статические веб-приложения**.
1. Выберите **Статические веб-приложения (предварительная версия)** .
1. Нажмите кнопку **Создать**.

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Создание ресурса "Статические веб-приложения Azure" на портале":::

1. Для параметра **Подписка** подтвердите предложенный вариант или выберите другой из раскрывающегося списка.

1. Для параметра _Группа ресурсов_ выберите **Создать**. В разделе _Новое имя группы ресурсов_ введите **hugo-static-app** и нажмите кнопку **ОК**.

1. Затем в поле **Имя** укажите глобально уникальное имя для приложения. Допустимые символы: `a-z`, `A-Z`, `0-9` и `-`. Это значение используется в качестве префикса URL-адреса для статического приложения в формате `https://<APP_NAME>....`.

1. В поле _Регион_выберите ближайший доступный регион.

1. В поле _Номер SKU_ выберите **Бесплатный**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Заполненные сведениями поля":::

1. Нажмите кнопку **Войти по учетным данным GitHub**.

1. В поле **Организация** выберите ту, в которой был создан репозиторий.

1. Выберите **hugo-static-app** в качестве _репозитория_.

1. В поле _Ветвь_ выберите **главная**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Поля, заполненные сведениями о GitHub":::

### <a name="build"></a>Сборка

Затем добавьте параметры конфигурации, используемые процессом сборки для компиляции приложения. Следующие параметры позволяют настроить файл рабочего процесса для действия GitHub.

1. Нажмите кнопку **Next: Build >** (Далее: сборка >) для изменения конфигурации сборки.

1. В поле _App location_ (Расположение приложения) задайте значение **public** (общедоступное).

1. Оставьте поле _App artifact location_ (Расположения артефакта приложения) пустым.

   Поле _API location_ (Расположения API) заполнять не требуется, так как вы в данный момент не развертываете API.

### <a name="review-and-create"></a>Просмотр и создание

1. Нажмите кнопку **Просмотр и создание** и убедитесь, что вы указали правильные сведения.

1. Щелкните **Создать**, чтобы начать создание приложения службы "Статические веб-приложения Azure", а также подготовить действие GitHub к развертыванию.

1. После завершения развертывания перейдите к терминалу и зафиксируйте изменения с помощью действия GitHub на компьютере.

   ```bash
   git pull
   ```

1. Откройте приложение Hugo в текстовом редакторе и откройте файл _.github/workflows/azure-pages-<WORKFLOW_NAME>.yml_.

1. чтобы создать приложение Hugo, замените строку `- uses: actions/checkout@v2` (строка 18) на указанную ниже.

   ```yml
   - uses: actions/checkout@v2
     with:
       submodules: true

   - name: Setup Hugo
     uses: peaceiris/actions-hugo@v2.4.8
     with:
       hugo-version: "latest"

   - name: Build
     run: hugo
   ```

1. Зафиксируйте обновленный рабочий процесс и отправьте его в GitHub.

   ```bash
   git add -A
   git commit -m "Updating GitHub Actions workflow"
   git push
   ```

1. Дождитесь завершения действия GitHub.

1. На портале Azure в окне _Обзор_ для только что созданного ресурса "Статические веб-приложения Azure" щелкните ссылку на _URL-адрес_, чтобы открыть развернутое приложение.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Развернутое приложение":::

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавить личный домен](custom-domain.md)
