---
title: Используйте действия GitHub для обновления кода в функциях Azure
description: Узнайте, как использовать действия GitHub для определения рабочего процесса для создания и развертывания проектов Azure Functions в GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649914"
---
# <a name="continuous-delivery-by-using-github-action"></a>Непрерывная доставка с помощью GitHub Action

[Действия GitHub](https://github.com/features/actions) позволяют определить рабочий процесс для автоматической сборки и развертывания кода функций для функции приложения в Azure. 

В GitHub Actions [рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) — это автоматизированный процесс, который вы определяете в репозитории GitHub. Этот процесс рассказывает GitHub о том, как создавать и развертывать проект приложения функций на GitHub. 

Рабочий процесс определяется файлом YAML (.yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, которые составляют рабочий процесс. 

Для рабочего процесса Функции Azure файл имеет три раздела: 

| Section | Задания |
| ------- | ----- |
| **Проверка подлинности** | <ol><li>Определите директора службы.</li><li>Скачать профиль публикации.</li><li>Создайте секрет GitHub.</li></ol>|
| **Построить** | <ol><li>Настройка среды.</li><li>Создайте приложение-функцию.</li></ol> |
| **Развернуть** | <ol><li>Развертывание приложения функции.</li></ol>|

> [!NOTE]
> Вам не нужно создавать основной службы, если вы решили использовать профиль публикации для проверки подлинности.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [основную службу,](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) используя команду [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI.](/cli/azure/) Вы можете запустить эту команду с помощью [оболочки Облака Azure](https://shell.azure.com) на портале Azure или выбрав кнопку **«Попробуй теску».**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

В этом примере замените заполнителей ресурса идентификатором подписки, группой ресурсов и именем приложения функции. Вывод — это учетные данные назначения ролей, которые обеспечивают доступ к приложению функции. Скопируйте этот объект JSON, который можно использовать для проверки подлинности от GitHub.

> [!IMPORTANT]
> Это всегда хорошая практика, чтобы предоставить минимальный доступ. Вот почему область в предыдущем примере ограничена конкретным приложением функции функции, а не всей группой ресурсов.

## <a name="download-the-publishing-profile"></a>Скачать профиль публикации

Вы можете скачать профиль публикации вашего приложения функции, перейдя на страницу **Обзор** вашего приложения и нажав **Получить опубликовать профиль**.

   ![Загрузить профиль публикации](media/functions-how-to-github-actions/get-publish-profile.png)

Копирование содержимого файла.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

1. В [GitHub,](https://github.com)просматривать репозиторий, выберите **Настройки** > **Секреты** > **Добавить новый секрет.**

   ![Добавить секрет](media/functions-how-to-github-actions/add-secret.png)

1. Добавьте новый секрет.

   * Если вы используете основной сервис, созданный с помощью `AZURE_CREDENTIALS` Azure CLI, используйте **имя**. Затем вставьте скопированный выход объекта JSON для **значения,** и выберите **Добавить секрет.**
   * Если вы используете профиль публикации, используйте `SCM_CREDENTIALS` **имя**. Затем используйте содержимое файла профиля публикации для **Value**и выберите **Добавить секрет.**

Теперь GitHub может проверить подлинность вашего функционального приложения в Azure.

## <a name="set-up-the-environment"></a>Настройка среды 

Настройка среды осуществляется с помощью действия настройки публикации, специфичном для языка.

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показана часть рабочего процесса, используюметодная `actions/setup-node` для настройки среды:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показана часть рабочего процесса, используюметодная `actions/setup-python` для настройки среды:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере показана часть рабочего процесса, используюметодная `actions/setup-dotnet` для настройки среды:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана часть рабочего процесса, используюметодная `actions/setup-java` для настройки среды:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Создание приложения функции

Это зависит от языка и языков, поддерживаемых Функциями Azure, этот раздел должен быть стандартным шагом сборки каждого языка.

В следующем примере показана часть рабочего процесса, которая создает приложение функции, которая является специфическим для языка:

# <a name="javascript"></a>[Javascript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C #](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Развертывание приложения-функции

Чтобы развернуть код в функциональном приложении, `Azure/functions-action` необходимо использовать действие. Это действие имеет два параметра:

|Параметр |Объяснение  |
|---------|---------|
|**_имя приложения_** | (Обязательно) Название приложения функции. |
|_**слот-имя**_ | (Необязательно) Название [слота развертывания,](functions-deployment-slots.md) в который вы хотите развернуть. Слот уже должен быть определен в приложении функции. |


В следующем примере используется `functions-action`версия 1:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы просмотреть полный рабочий процесс .yaml, см. один из файлов в `functionapp` [примерах рабочего процесса Azure GitHub,](https://aka.ms/functions-actions-samples) которые имеют в названии. Эти образцы можно использовать в отправной точкой для рабочего процесса.

> [!div class="nextstepaction"]
> [Подробнее о действиях GitHub](https://help.github.com/en/articles/about-github-actions)
