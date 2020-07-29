---
title: Использование действий GitHub для внесения обновлений в код в функциях Azure
description: Узнайте, как использовать действия GitHub, чтобы определить рабочий процесс для создания и развертывания проектов функций Azure в GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 1a7cc37f297f902fb5de473303f1dc260cbea9ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559107"
---
# <a name="continuous-delivery-by-using-github-action"></a>Непрерывная поставка с помощью действия GitHub

[Действия GitHub](https://github.com/features/actions) позволяют определить рабочий процесс для автоматической сборки и развертывания кода функций в приложении-функции в Azure. 

В действиях GitHub [Рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) — это автоматизированный процесс, который определяется в репозитории GitHub. Этот процесс говорит GitHub о том, как создать и развернуть проект приложения функций на GitHub. 

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса. 

Для рабочего процесса функций Azure файл содержит три раздела: 

| Section | Задания |
| ------- | ----- |
| **Аутентификация** | <ol><li>Определите субъект-службу.</li><li>Скачивание профиля публикации.</li><li>Создайте секрет GitHub.</li></ol>|
| **Сборка** | <ol><li>Настройте среду.</li><li>Создайте приложение-функцию.</li></ol> |
| **Развертывание** | <ol><li>Разверните приложение функции.</li></ol>|

> [!NOTE]
> Не нужно создавать субъект-службу, если вы решили использовать профиль публикации для проверки подлинности.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](/cli/azure/). Эту команду можно выполнить в [Azure Cloud Shell](https://shell.azure.com) на портале Azure или с помощью кнопки **Попробовать**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

В этом примере Замените заполнители в ресурсе ИДЕНТИФИКАТОРом подписки, группой ресурсов и именем приложения функции. Выходные данные — это учетные данные назначения роли, которые предоставляют доступ к приложению-функции. Скопируйте этот объект JSON, который затем можно использовать для выполнения аутентификации из GitHub.

> [!IMPORTANT]
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Именно поэтому область в предыдущем примере ограничена конкретным приложением-функцией, а не всей группой ресурсов.

## <a name="download-the-publishing-profile"></a>Скачивание профиля публикации

Чтобы скачать профиль публикации приложения функции, выполните следующие действия.

1. Выберите страницу **Обзор** приложения-функции и щелкните **получить профиль публикации**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Загрузить профиль публикации":::

1. Сохраните и скопируйте содержимое файла параметров публикации.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

1. В [GitHub](https://github.com)перейдите к репозиторию, выберите **Параметры**  >  **секреты**  >  **Добавить новый секрет**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Добавить секрет":::

1. Добавьте новый секрет.

   * Если вы используете субъект-службу, созданный с помощью Azure CLI, используйте `AZURE_CREDENTIALS` для **имени**. Затем вставьте скопированный выходной объект JSON в поле **значение**и выберите **Добавить секрет**.
   * Если вы используете профиль публикации, используйте `SCM_CREDENTIALS` для **имени**. Затем используйте содержимое файла профиля публикации в поле **значение**и выберите **Добавить секрет**.

Теперь GitHub может проходить проверку подлинности в приложении функции в Azure.

## <a name="set-up-the-environment"></a>Настройка среды 

Настройка среды выполняется с помощью действия по настройке публикации для конкретного языка.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана часть рабочего процесса, которая использует `actions/setup-node` действие для настройки среды.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показана часть рабочего процесса, которая использует `actions/setup-python` действие для настройки среды.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана часть рабочего процесса, которая использует `actions/setup-dotnet` действие для настройки среды.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана часть рабочего процесса, которая использует `actions/setup-java` действие для настройки среды.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
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

Это зависит от языка и языков, поддерживаемых функциями Azure. Этот раздел должен быть стандартным этапом сборки каждого языка.

В следующем примере показана часть рабочего процесса, который создает приложение-функцию, которое зависит от языка:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="c"></a>[C#](#tab/csharp)

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

Чтобы развернуть код в приложении-функции, необходимо будет использовать `Azure/functions-action` действие. Это действие имеет два параметра:

|Параметр |Объяснение  |
|---------|---------|
|**_app-name_** | Заполнен Имя приложения функции. |
|_**slot-name**_ | Используемых Имя [слота развертывания](functions-deployment-slots.md) , в который требуется выполнить развертывание. Слот уже должен быть определен в приложении функции. |


В следующем примере используется версия 1 из `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы просмотреть полный файл Workflow. YAML, ознакомьтесь с одним из файлов в [репозитории примеров рабочих процессов Azure GitHub](https://aka.ms/functions-actions-samples) , имеющих `functionapp` имя. Эти примеры можно использовать в качестве отправной точки для рабочего процесса.

> [!div class="nextstepaction"]
> [Дополнительные сведения о действиях GitHub](https://help.github.com/en/articles/about-github-actions)
