---
title: Настройка CI/CD с помощью GitHub Actions
description: Узнайте, как развернуть код в Службе приложений Azure из конвейера CI/CD с помощью GitHub Actions. Настройте задачи создания и выполняйте сложные развертывания.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure
ms.openlocfilehash: 2d28d8f1f09814822b29e9d45d4e75283c8955cc
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618749"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Развертывание в Службе приложений с помощью GitHub Actions

Начните работу с [действиями GitHub](https://help.github.com/en/articles/about-github-actions) , чтобы автоматизировать рабочий процесс и развернуть его в [службе приложений Azure](overview.md) из GitHub. 

## <a name="prerequisites"></a>Предварительные требования 

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  
- Рабочее приложение службы приложений Azure. 
    - .NET: [Создание веб-приложения ASP.NET Core в Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Создание веб-приложения ASP.NET Framework в Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Создание веб-приложения Node.js в службе приложений Azure](quickstart-nodejs.md)  
    - Java: [Создание приложения Java в службе приложений Azure](quickstart-java.md)
    - Python: [Создание приложения Python в службе приложений Azure](quickstart-python.md)

## <a name="workflow-file-overview"></a>Общие сведения о файле рабочего процесса

Файлы рабочих процессов службы приложений Azure имеют три раздела:

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса.

Файл содержит три раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определите субъект-службу или профиль публикации. <br /> 2. Создайте секрет GitHub. |
|**Сборка** | 1. Настройте среду. <br /> 2. Создайте веб-приложение. |
|**Развертывание** | 1. Разверните веб-приложение. |

## <a name="use-the-deployment-center"></a>Использование центра развертывания

Вы можете быстро начать работу с действиями GitHub с помощью центра развертывания службы приложений. Это автоматически создаст файл рабочего процесса на основе стека приложения и зафиксирует его в репозитории GitHub в правильном каталоге.

1. Перейдите к webapp в портал Azure
1. В левой части щелкните **центр развертывания** .
1. В разделе **непрерывное развертывание (CI/CD)** выберите **GitHub** .
1. Затем выберите **действия GitHub**
1. Используйте раскрывающиеся списки, чтобы выбрать репозиторий GitHub, ветвь и стек приложений
    - Если выбранная ветвь защищена, вы по-прежнему можете продолжить добавление файла рабочего процесса. Прежде чем продолжить, проверьте защиту ветвей.
1. На последнем экране вы можете просмотреть выбранное значение и просмотреть файл рабочего процесса, который будет зафиксирован в репозитории. Если выбраны правильные значения, нажмите кнопку **Готово** .

Файл рабочего процесса будет зафиксирован в репозитории. Рабочий процесс для сборки и развертывания приложения будет запущен немедленно.

## <a name="set-up-a-work-manually"></a>Настройка работы вручную

Вы также можете развернуть рабочий процесс без использования центра развертывания. Для этого необходимо сначала создать учетные данные развертывания. 

## <a name="generate-deployment-credentials"></a>Создать учетные данные развертывания

Рекомендуемый способ проверки подлинности в службе приложений Azure для действий GitHub — профиль публикации. Вы также можете пройти проверку подлинности с помощью субъекта-службы, но процесс требует дополнительных действий. 

Сохраните учетные данные профиля публикации или субъекта-службы в качестве [секрета GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) для аутентификации в Azure. Вы получите доступ к секрету в рабочем процессе. 

# <a name="publish-profile"></a>[Опубликовать профиль](#tab/applevel)

Профиль публикации — это учетные данные на уровне приложения. Настройте профиль публикации в качестве секрета GitHub. 

1. Перейдите к службе приложений в портал Azure. 

1. На странице **Обзор** выберите **получить профиль публикации**.

1. Сохраните скачанный файл. Вы будете использовать содержимое файла для создания секрета GitHub.

# <a name="service-principal"></a>[Субъект-служба](#tab/userlevel)

Вы можете создать [субъект-службу](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) с помощью команды [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) в [Azure CLI](/cli/azure/). Выполните эту команду с [Azure Cloud Shell](https://shell.azure.com/) в портал Azure или нажав кнопку **попробовать** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

В приведенном выше примере Замените заполнители ИДЕНТИФИКАТОРом подписки, именем группы ресурсов и именем приложения. Выходные данные — это объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению службы приложений, как показано ниже. Скопируйте этот объект JSON для последующего использования.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Рекомендуется всегда предоставлять минимальные разрешения доступа. Область в предыдущем примере ограничена конкретным приложением службы приложений, а не всей группой ресурсов.

---

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub


# <a name="publish-profile"></a>[Опубликовать профиль](#tab/applevel)

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Чтобы использовать [учетные данные уровня приложения](#generate-deployment-credentials), вставьте содержимое скачанного файла профиля публикации в поле значение секрета. Назовите секрет `AZURE_WEBAPP_PUBLISH_PROFILE` .

При настройке рабочего процесса GitHub используйте `AZURE_WEBAPP_PUBLISH_PROFILE` в действии развертывание веб-приложения Azure. Пример:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Субъект-служба](#tab/userlevel)

В [GitHub](https://github.com/)найдите репозиторий, выберите **параметры > секреты > добавить новый секрет**.

Чтобы использовать [учетные данные на уровне пользователя](#generate-deployment-credentials), вставьте все выходные данные JSON из команды Azure CLI в поле значения секрета. Присвойте секрету имя, например `AZURE_CREDENTIALS` .

Когда вы настроите файл рабочего процесса позже, используйте секрет для входа `creds` в действие Azure Login. Пример:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Настройка среды

Настройку среды можно выполнить с помощью одного из следующих действий.

|**Язык**  |**Действие настройки**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

В следующих примерах показано, как настроить среду для различных поддерживаемых языков:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Создание веб-приложения

Процесс создания веб-приложения и развертывания в службе приложений Azure изменяется в зависимости от языка. 

В следующих примерах показана часть рабочего процесса, который создает веб-приложение на разных поддерживаемых языках.

Для всех языков можно задать корневой каталог веб-приложения с помощью `working-directory` . 

**.NET**

Переменная среды `AZURE_WEBAPP_PACKAGE_PATH` задает путь к проекту веб-приложения. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Вы можете восстановить зависимости NuGet и запустить MSBuild с помощью `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Для Node.js можно задать `working-directory` или изменить для каталога NPM в `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Развертывание в службу приложений

Чтобы развернуть код в приложении Службы приложений, используйте действие `azure/webapps-deploy@v2`. Это действие имеет четыре параметра:

| **Параметр**  | **Пояснение**  |
|---------|---------|
| **app-name** | (Обязательный) Имя приложения Службы приложений. | 
| **publish-profile** | (Необязательный) Содержимое файла профиля публикации с секретами для веб-развертывания. |
| **package** | (Необязательный) Путь к пакету или папке. Путь может включать *. zip, *. war, *. jar или папку для развертывания |
| **slot-name** | Используемых Введите существующий слот, отличный от рабочего [слота](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Опубликовать профиль](#tab/applevel)

### <a name="net-core"></a>.NET Core

Создание и развертывание приложения .NET Core в Azure с помощью профиля публикации Azure. `publish-profile`Входные данные ссылаются на `AZURE_WEBAPP_PUBLISH_PROFILE` секрет, созданный ранее.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Создание и развертывание приложения ASP.NET MVC, которое использует NuGet и `publish-profile` для проверки подлинности. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Создание и развертывание пружинного приложения Java в Azure с помощью профиля публикации Azure. `publish-profile`Входные данные ссылаются на `AZURE_WEBAPP_PUBLISH_PROFILE` секрет, созданный ранее.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Чтобы развернуть `war` вместо `jar` , измените `package` значение. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Создание и развертывание Node.js приложения в Azure с помощью профиля публикации приложения. `publish-profile`Входные данные ссылаются на `AZURE_WEBAPP_PUBLISH_PROFILE` секрет, созданный ранее.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Создание и развертывание приложения Python в Azure с помощью профиля публикации приложения. Обратите внимание, как `publish-profile` входные данные ссылаются на `AZURE_WEBAPP_PUBLISH_PROFILE` секрет, созданный ранее.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2-beta
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Субъект-служба](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Создание и развертывание приложения .NET Core в Azure с помощью субъекта-службы Azure. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Создание и развертывание приложения ASP.NET MVC в Azure с помощью субъекта-службы Azure. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Создание и развертывание пружинного приложения Java в Azure с помощью субъекта-службы Azure. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Создание и развертывание Node.js приложения в Azure с помощью субъекта-службы Azure. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

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
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Создание и развертывание приложения Python в Azure с помощью субъекта-службы Azure. Обратите внимание, как `creds` входные данные ссылаются на `AZURE_CREDENTIALS` секрет, созданный ранее.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти наш набор компонентов Actions, объединенных в разных репозитории, а также соответствующую документацию и примеры использования GitHub для CI/CD и развертыванию приложений в Azure на GitHub.

- [Рабочие процессы действий для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)

- [Вход в Azure](https://github.com/Azure/login)

- [Веб-приложение Azure](https://github.com/Azure/webapps-deploy)

- [Веб-приложение Azure для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Вход в Docker и выход из него](https://github.com/Azure/docker-login)

- [События, инициирующие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание Kubernetes](https://github.com/Azure/k8s-deploy)

- [Начальные рабочие процессы](https://github.com/actions/starter-workflows)
