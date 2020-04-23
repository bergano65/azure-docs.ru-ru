---
title: Настройка CI/CD с помощью действий GitHub
description: Узнайте, как развернуть код в службе приложений Azure из конвейера CI/CD с помощью действий GitHub. Настройка задач сборки и выполнение сложных развертываний.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084997"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Развертывание в службе приложений с помощью действий GitHub

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) позволяют вам гибко создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. С помощью действий службы приложений Azure для GitHub можно автоматизировать рабочий процесс для развертывания в [службе приложений Azure](overview.md) с помощью действий GitHub.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Вы должны сначала [зарегистрироваться, чтобы присоединиться к предварительному просмотру](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (.yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, которые составляют рабочий процесс.

Для рабочего процесса службы приложений Azure файл имеет три раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определить директора службы <br /> 2. Создайте секрет GitHub |
|**Сборка** | 1. Настройка окружающей среды <br /> 2. Создайте веб-приложение |
|**Развернуть** | 1. Развертывание веб-приложения |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [основную службу,](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) используя команду [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI.](https://docs.microsoft.com/cli/azure/) Вы можете запустить эту команду с помощью [оболочки Облака Azure](https://shell.azure.com/) на портале Azure или выбрав кнопку **«Попробуй теску».**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

В этом примере замените заполнителей ресурса идентификатором подписки, именем группы ресурсов и именем приложения. Выход — это учетные данные назначения ролей, которые обеспечивают доступ к приложению Службы App. Скопируйте этот объект JSON, который можно использовать для проверки подлинности от GitHub.

> [!NOTE]
> Вам не нужно создавать основной службы, если вы решили использовать профиль публикации для проверки подлинности.

> [!IMPORTANT]
> Это всегда хорошая практика, чтобы предоставить минимальный доступ. Вот почему область в предыдущем примере ограничена конкретным приложением App Service, а не всей группой ресурсов.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

Можно также использовать учетные данные уровня приложения, т.е. публиковать профиль для развертывания. Выполните действия по настройке секрета:

1. Загрузите профиль публикации для приложения App Service с портала, используя опцию **профиля Get Publish.**

2. В [GitHub](https://github.com/)просмотрите репозиторий, выберите **Настройки > Секреты > Добавить новый секрет**

    ![секретные коды](media/app-service-github-actions/secrets.png)

3. Вставьте содержимое для загруженного файла профиля в поле значения секрета.

4. Теперь в файле рабочего `.github/workflows/workflow.yml` процесса в вашей `publish-profile` ветке: замените секрет для ввода развертывания действия Web App Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Вы видите секрет, как показано ниже, когда-то определено.

    ![секретные коды](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Настройка среды

Настройка среды может быть выполнена с помощью одного из действий настройки.

|**Язык**  |**Настройка действий**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Ниже приведены следующие примеры, в которой показана часть рабочего процесса, которая настраивает среду для различных поддерживаемых языков:

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

Это зависит от языка и языков, поддерживаемых Службой приложений Azure, этот раздел должен быть стандартным шагом сборки каждого языка.

Ниже приведены следующие примеры, в какой части рабочего процесса, который создает веб-приложение, на различных поддерживаемых языках.

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
## <a name="deploy-to-app-service"></a>Развертывание в Службе приложений

Чтобы развернуть код в приложении `azure/webapps-deploy@v2` Службы app, используйте действие. Это действие имеет четыре параметра:

| **Параметр**  | **Объяснение**  |
|---------|---------|
| **имя приложения** | (Обязательно) Название приложения Службы Приложений | 
| **опубликовать-профиль** | (Необязательно) Публикация содержимого файла профиля с помощью секретов Web Deploy |
| **package** | (Необязательно) Путь к пакету или папке. З.зип, З.Война, К.банк или папка для развертывания |
| **слот-имя** | (Необязательно) Введите существующий слот, кроме слота Production |

### <a name="deploy-using-publish-profile"></a>Развертывание с помощью профиля публикации

Ниже приведен пример рабочего процесса для создания и развертывания приложения Node.js в Azure с помощью профиля публикации.

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

### <a name="deploy-using-azure-service-principal"></a>Развертывание с помощью основного сервиса Azure

Ниже приведен пример рабочего процесса для создания и развертывания приложения Node.js в Azure с помощью принципа службы Azure.

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

Вы можете найти наш набор действий, сгруппированных в различные репозитории на GitHub, каждый из которых содержит документацию и примеры, чтобы помочь вам использовать GitHub для CI/CD и развернуть свои приложения в Azure.

- [Рабочий процесс действий для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Докер войти / выход](https://github.com/Azure/docker-login)

- [События, запускающие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание K8](https://github.com/Azure/k8s-deploy)

- [Стартовые рабочие процессы](https://github.com/actions/starter-workflows)
