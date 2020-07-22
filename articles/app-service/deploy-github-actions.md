---
title: Настройка CI/CD с помощью GitHub Actions
description: Узнайте, как развернуть код в Службе приложений Azure из конвейера CI/CD с помощью GitHub Actions. Настройте задачи создания и выполняйте сложные развертывания.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: tracking-python
ms.openlocfilehash: b40da0c8746bc63a99394027b61d777a611727e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559593"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Развертывание в Службе приложений с помощью GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) предоставляет гибкие возможности для создания автоматизированных рабочих процессов жизненного цикла разработки программного обеспечения. С помощью GitHub Actions вы можете автоматизировать рабочий процесс для развертывания в [Службе приложений Azure](overview.md).

> [!IMPORTANT]
> GitHub Actions в настоящее время предоставляется в виде бета-версии. Сначала вам необходимо [зарегистрироваться для присоединения к предварительной версии](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса.

Для рабочего процесса Службы приложений Azure файл содержит три раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определение субъекта-службы <br /> 2. Создание секрета GitHub |
|**Сборка** | 1. Настройка среды <br /> 2. Создание веб-приложения |
|**Развертывание** | 1. Развертывание веб-приложения |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI](https://docs.microsoft.com/cli/azure/). Эту команду можно выполнить в [Azure Cloud Shell](https://shell.azure.com/) на портале Azure или с помощью кнопки **Попробовать**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

В этом примере замените заполнители в ресурсе своими значениями для идентификатора подписки, имени группы ресурсов и имени приложения. В выходных данных вы получите учетные данные назначения роли, предоставляющие доступ к приложению Службы приложений. Скопируйте этот объект JSON, который затем можно использовать для выполнения аутентификации из GitHub.

> [!NOTE]
> Вам не нужно создавать субъект-службу, если для аутентификации вы хотите использовать профиль публикации.

> [!IMPORTANT]
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Именно поэтому область в предыдущем примере ограничена конкретным приложением Службы приложений, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

Вы также можете использовать учетные данные уровня приложения, например профиль публикации для развертывания. Выполните следующие действия, чтобы настроить секрет:

1. Скачайте профиль публикации для приложения Службы приложений на портале, щелкнув **Получить профиль публикации**.

2. В [GitHub](https://github.com/) перейдите к репозиторию и выберите **Settings > Secrets > Add a new secret** (Параметры > Секреты > Добавить секрет).

    ![секретные коды](media/app-service-github-actions/secrets.png)

3. Вставьте содержимое скачанного файла профиля публикации в поле значения секрета.

4. Теперь в файле рабочего процесса в ветви `.github/workflows/workflow.yml` замените секрет для входного профиля `publish-profile` для действия развертывания веб-приложения Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. После определения секрет будет отображаться, как показано ниже.

    ![секретные коды](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Настройка среды

Настройку среды можно выполнить с помощью одного из следующих действий.

|**Язык**  |**Действие настройки**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

В следующих примерах показана часть рабочего процесса, которая настраивает среду для разных поддерживаемых языков.

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

Этот процесс зависит от языка. Для языков, поддерживаемых Службой приложений Azure, инструкции в этом разделе выполняются в рамках в стандартной процедуры.

В следующих примерах показана часть рабочего процесса, которая создает веб-приложение на разных поддерживаемых языках.

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

Чтобы развернуть код в приложении Службы приложений, используйте действие `azure/webapps-deploy@v2`. Это действие имеет четыре параметра:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **app-name** | (Обязательный) Имя приложения Службы приложений. | 
| **publish-profile** | (Необязательный) Содержимое файла профиля публикации с секретами для веб-развертывания. |
| **package** | (Необязательный) Путь к пакету или папке. Файлы ZIP, WAR, JAR или папка для развертывания. |
| **slot-name** | (Необязательный) Введите существующий слот вместо рабочего. |

### <a name="deploy-using-publish-profile"></a>Развертывание с помощью профиля публикации

Ниже приведен пример рабочего процесса для сборки и развертывания приложения Node.js в Azure с помощью профиля публикации.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Развертывание с помощью субъекта-службы Azure

Ниже приведен пример рабочего процесса для сборки и развертывания приложения Node.js в Azure с помощью субъекта-службы.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти наш набор компонентов Actions, объединенных в разных репозитории, а также соответствующую документацию и примеры использования GitHub для CI/CD и развертыванию приложений в Azure на GitHub.

- [Рабочий процесс действия для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)

- [Веб-приложение Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в Docker и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)
