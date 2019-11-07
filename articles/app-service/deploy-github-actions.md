---
title: Развертывание кода из конвейера CI/CD с помощью действий GitHub в службе приложений Azure | Документация Майкрософт
description: Узнайте, как использовать действия GitHub для развертывания кода в службе приложений.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 9842057a590b08f2207a1ea166e0ce0d457e4381
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620515"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Развертывание в службе приложений с помощью действий GitHub

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) дают возможность создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. Действия службы приложений Azure для GitHub позволяют автоматизировать рабочий процесс для развертывания в [службе приложений Azure](overview.md) с помощью действий GitHub.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Сначала необходимо [зарегистрироваться, чтобы присоединиться к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (yml) в пути `/.github/workflows/` в репозитории. Это определение содержит различные шаги и параметры, составляющие рабочий процесс.

Для рабочего процесса службы приложений Azure файл содержит три раздела:

|Раздел  |Задачи  |
|---------|---------|
|**Проверка подлинности** | 1. Определение субъекта-службы <br /> 2. Создание секрета GitHub |
|**Сборка** | 1. Настройка среды <br /> 2. Создание веб-приложения |
|**Развертывание** | 1. Развертывание веб-приложения |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](https://docs.microsoft.com/cli/azure/). Эту команду можно выполнить с помощью [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

В этом примере Замените заполнители в ресурсе ИДЕНТИФИКАТОРом подписки, именем группы ресурсов и именем приложения. Выходные данные — это учетные данные назначения роли, которые предоставляют доступ к приложению службы приложений. Скопируйте этот объект JSON, который можно использовать для проверки подлинности из GitHub.

> [!NOTE]
> Не нужно создавать субъект-службу, если вы решили использовать профиль публикации для проверки подлинности.

> [!IMPORTANT]
> Всегда рекомендуется предоставлять минимальный доступ. Именно поэтому область в предыдущем примере ограничена конкретным приложением службы приложений, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

Можно также использовать учетные данные уровня приложения, например опубликовать профиль для развертывания. Выполните действия по настройке секрета.

1. Скачайте профиль публикации для приложения службы приложений с портала с помощью команды **получить профиль публикации** .

2. В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > Добавить новый секрет**

    ![секретные коды](media/app-service-github-actions/secrets.png)

3. Вставьте содержимое скачанного файла профиля публикации в поле значение секрета.

4. Теперь в файле рабочего процесса в ветви: `.github/workflows/workflow.yml` замените секрет для входных `publish-profile` действия "развертывание веб-приложения Azure".
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Вы видите секретный код, как показано ниже.

    ![секретные коды](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Настройка среды

Настройку среды можно выполнить с помощью одного из действий программы установки.

|**Язык**  |**Действие установки**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

В следующих примерах показана часть рабочего процесса, которая настраивает среду для различных поддерживаемых языков:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Создание веб-приложения

Это зависит от языка и языков, поддерживаемых службой приложений Azure. Этот раздел должен быть стандартным этапом сборки каждого языка.

В следующих примерах показана часть рабочего процесса, который создает веб-приложение на различных поддерживаемых языках.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
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
        # If your web app project is not located in your repository's root
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
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Развертывание в службу приложений

Чтобы развернуть код в приложении службы приложений, используйте действие `azure/webapps-deploy@v1 `. Это действие имеет четыре параметра:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **имя приложения** | Необходимости Имя приложения службы приложений | 
| **Публикация-профиль** | Используемых Публикация содержимого файла профиля с веб-развертывание секретами |
| **package** | Используемых Путь к пакету или папке. *. zip, *. war, *. jar или папка для развертывания |
| **имя слота** | Используемых Введите существующий слот, отличный от рабочего слота |

### <a name="deploy-using-publish-profile"></a>Развертывание с помощью профиля публикации

Ниже приведен пример рабочего процесса для сборки и развертывания приложения Node. js в Azure с помощью профиля публикации.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Развертывание с помощью субъекта-службы Azure

Ниже приведен пример рабочего процесса для создания и развертывания приложения Node. js в Azure с помощью субъекта-службы Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Дальнейшие действия

Наш набор действий, сгруппированных в различные репозитории в GitHub, можно найти в каждом из них, содержащих документацию и примеры, которые помогут вам использовать GitHub для непрерывной интеграции и развертывания приложений в Azure.

- [Рабочий процесс действий для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в DOCKER и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание K8s](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)
