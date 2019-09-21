---
title: Использование действий GitHub для внесения обновлений в код в функциях Azure
description: Узнайте, как использовать действия GitHub, чтобы определить рабочий процесс для создания и развертывания проектов функций Azure в GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 2d97e46578775cd0d5612fb8d94680bb5be8080a
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174777"
---
# <a name="continuous-delivery-by-using-github-action"></a>Непрерывная поставка с помощью действия GitHub

[Действия GitHub](https://github.com/features/actions) позволяют определить рабочий процесс для автоматической сборки и развертывания кода функций в приложении-функции в Azure. 

> [!IMPORTANT]  
> Действия GitHub в настоящее время находятся в бета-версии. Сначала необходимо [зарегистрироваться, чтобы присоединиться к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.

В действиях GitHub [Рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) — это автоматизированный процесс, который определяется в репозитории GitHub. Этот процесс говорит GitHub о том, как создать и развернуть проект приложения функций на GitHub. 

Рабочий процесс определяется файлом YAML (. yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, составляющие рабочий процесс. 

Для рабочего процесса функций Azure файл содержит три раздела: 

| `Section` | Задачи |
| ------- | ----- |
| **Authentication** | <ol><li>Определите субъект-службу.</li><li>Создайте секрет GitHub.</li></ol>|  
| **Сборка** | <ol><li>Настройте среду.</li><li>Создайте приложение-функцию.</li></ol> |
| **Развертывание** | <ol><li>Разверните приложение функции.</li></ol>| 

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](/cli/azure/). Эту команду можно выполнить с помощью [Azure Cloud Shell](https://shell.azure.com) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

В этом примере Замените заполнители в ресурсе ИДЕНТИФИКАТОРом подписки, группой ресурсов и именем приложения функции. Выходные данные — это учетные данные назначения роли, которые обеспечивают доступ к приложению-функции. Скопируйте этот объект JSON, который можно использовать для проверки подлинности из GitHub.

> [!IMPORTANT]
> Всегда рекомендуется предоставлять минимальный доступ. Именно поэтому область в предыдущем примере ограничена конкретным приложением-функцией, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

1. В [GitHub](https://github/com)найдите репозиторий, выберите **Параметры** > **секреты** > **Добавить новый секрет**.

    ![Добавить секрет](media/functions-how-to-github-actions/add-secret.png)

1. Используйте `AZURE_CREDENTIALS` для параметра **имя** и скопированный результат команды для **значения**, а затем выберите **Добавить секрет**. 

Теперь GitHub может проходить проверку подлинности в приложении функции в Azure.

## <a name="set-up-the-environment"></a>Настройка среды 

Настройка среды может быть выполнена с помощью одного из действий программы установки публикации.

|Язык | Действие установки |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

В следующих примерах показана часть рабочего процесса, которая настраивает среду для различных поддерживаемых языков:

**JavaScript**

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

**Python**

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

**.NET**

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

**Java**

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

## <a name="build-the-function-app"></a>Создание приложения функции

Это зависит от языка и языков, поддерживаемых функциями Azure. Этот раздел должен быть стандартным этапом сборки каждого языка.

В следующих примерах показана часть рабочего процесса, который строит приложение-функцию на различных поддерживаемых языках.

**JavaScript**

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

**Python**

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

**.NET**

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

**Java**

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

## <a name="deploy-the-function-app"></a>Развертывание приложения-функции

Чтобы развернуть код в приложении-функции, необходимо будет использовать `Azure/functions-action` действие. Это действие имеет два параметра:

|Параметр |Объяснение  |
|---------|---------|
|**_имя приложения_** | Заполнен Имя приложения функции. |
|_**имя слота**_ | Используемых Имя [слота развертывания](functions-deployment-slots.md) , в который требуется выполнить развертывание. Слот уже должен быть определен в приложении функции. |


В следующем примере используется версия 1 из `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Следующие шаги

Чтобы просмотреть полный рабочий процесс. YAML, ознакомьтесь с одним из файлов в [репозитории примеров рабочих процессов Azure GitHub](https://github.com/Azure/actions-workflow-samples) , имеющих `functionapp` имя. Эти примеры можно использовать в качестве отправной точки для рабочего процесса.

> [!div class="nextstepaction"]
> [Дополнительные сведения о действиях GitHub](https://help.github.com/en/articles/about-github-actions)
