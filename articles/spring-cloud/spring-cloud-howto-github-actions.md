---
title: Облачный CI/CD Azure Spring с действиями GitHub
description: Как создать рабочий процесс CI/CD для весеннего облака Azure с помощью действий GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538470"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Облачный CI/CD Azure Spring с действиями GitHub

GitHub Actions поддерживает автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. С помощью GitHub Actions для Spring Cloud можно создавать рабочие процессы в репозитории для создания, тестирования, обновления, выпуска и развертывания в Azure. 

## <a name="prerequisites"></a>Предварительные требования
Этот пример требует [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="set-up-github-repository-and-authenticate"></a>Настройка репозитория GitHub и аутентификация
Для авторизации действия входа в Azure требуется учетный учетный элемент принципа службы Azure. Чтобы получить учетные данные Azure, выполните следующие команды на локальной машине:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Чтобы получить доступ к определенной группе ресурсов, можно уменьшить область действия:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Команда должна выдвить объект JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

В этом примере используется образец [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) на GitHub.  Вилка образца, открыть страницу репозитория GitHub, и нажмите **Настройки вкладке.** Открытые **секреты** меню, и нажмите **Добавить новый секрет:**

 ![Добавить новый секрет](./media/github-actions/actions1.png)

Установите секретное `AZURE_CREDENTIALS` имя и его значение строке JSON, найденной под заголовком *Настройка репозитория GitHub и проверка подлинности.*

 ![Установка секретных данных](./media/github-actions/actions2.png)

Вы также можете получить учетные данные для входа в Azure из Key Vault в действиях GitHub, как это объясняется в [Authenticate Azure Spring с Key Vault в действиях GitHub.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Экземпляр предоставления услуг
Чтобы предоставить экземпляр службы облака Azure Spring, запустите следующие команды с помощью Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Создание рабочего процесса
Рабочий процесс определяется с помощью следующих параметров.

### <a name="prepare-for-deployment-with-azure-cli"></a>Подготовка к развертыванию с помощью Azure CLI
Команда `az spring-cloud app create` в настоящее время не идемпотент.  Мы рекомендуем этот рабочий процесс для существующих приложений и экземпляров Cloud Azure Spring.

Используйте следующие команды Azure CLI для подготовки:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Развертывание с Azure CLI напрямую
Создайте `.github/workflow/main.yml` файл в репозитории:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Развертывание с помощью действия Azure CLI
Команда `run` az будет использовать последнюю версию Azure CLI. При нарушении изменений можно также использовать конкретную версию Azure CLI с azure/CLI. `action` 

> [!Note] 
> Эта команда будет работать в `env` новом контейнере, поэтому не будет работать, и перекрестный доступ к файлам действия может иметь дополнительные ограничения.

Создайте файл .github/workflow/main.yml в репозитории:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Развертывание с Maven Plugin
Другой вариант заключается в использовании [Maven Plugin](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) для развертывания Jar и обновления настроек приложения. Команда `mvn azure-spring-cloud:deploy` является идемпотентной и автоматически создает приложения, если это необходимо. Вам не нужно создавать соответствующие приложения заранее.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Запуск рабочего процесса
**Действия** GitHub должны быть включены автоматически после нажатия `.github/workflow/main.yml` на GitHub. Действие будет срабатывать при нажатии нового коммита. Если вы создаете этот файл в браузере, ваше действие должно быть уже запущено.

Чтобы убедиться в включении действия, щелкните вкладку **«Действия»** на странице репозитория GitHub:

 ![Проверка включенных действий](./media/github-actions/actions3.png)

Если действие выполняется по ошибке, например, если вы не установили учетные данные Azure, можно повторно выполнить проверки после исправления ошибки. На странице репозитория GitHub щелкните **«Действия»,** выберите конкретную задачу рабочего процесса, а затем нажмите кнопку **«Перезапуск»** для повторного выполнения проверок:

 ![Повторная проверка](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Key Vault для действий весеннего облака GitHub](./spring-cloud-github-actions-key-vault.md)
* [Принципы активных служб каталогов Azure](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Действия GitHub для Azure](https://github.com/Azure/actions/)
