---
title: Пользовательский контейнер CI/CD от действий GitHub
description: Узнайте, как использовать Действия GitHub для развертывания пользовательского контейнера Linux в Службу поддержки приложений из конвейера CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246970"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Развертывание пользовательского контейнера в Службу поддержки приложений с помощью действий GitHub

[Действия GitHub](https://help.github.com/en/articles/about-github-actions) позволяют вам гибко создавать автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. С [помощью службы поддержки приложений Azure Action для контейнеров](https://github.com/Azure/webapps-container-deploy)можно автоматизировать рабочий процесс для развертывания приложений в качестве [пользовательских контейнеров для службы приложений](https://azure.microsoft.com/services/app-service/containers/) с помощью действий GitHub.

> [!IMPORTANT]
> Действия GitHub в настоящее время находятся в бета-версии. Вы должны сначала [зарегистрироваться, чтобы присоединиться к предварительному просмотру](https://github.com/features/actions) с помощью учетной записи GitHub.
> 

Рабочий процесс определяется файлом YAML (.yml) в `/.github/workflows/` пути в репозитории. Это определение содержит различные шаги и параметры, которые составляют рабочий процесс.

Для рабочего процесса контейнера Azure App Service файл имеет три раздела:

|Section  |Задания  |
|---------|---------|
|**Проверка подлинности** | 1. Определите директора службы. <br /> 2. Создайте секрет GitHub. |
|**Построить** | 1. Настройка окружающей среды. <br /> 2. Создайте изображение контейнера. |
|**Развернуть** | 1. Развертывание изображения контейнера. |

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать [основную службу,](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) используя команду [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) в [Azure CLI.](https://docs.microsoft.com/cli/azure/) Вы можете запустить эту команду с помощью [оболочки Облака Azure](https://shell.azure.com/) на портале Azure или выбрав кнопку **«Попробуй теску».**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Вывод — это объект JSON с учетными данными назначения ролей, которые обеспечивают доступ к приложению App Service, аналогичному приведенной ниже. Копирование этого объекта JSON для проверки подлинности с GitHub.

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
> Это всегда хорошая практика, чтобы предоставить минимальный доступ. Можно ограничить область действия вышеуказанной команды Az CLI конкретным приложением App Service и реестром контейнеров Azure, куда выталкиваются изображения контейнеров.

## <a name="configure-the-github-secret"></a>Настройка секрета GitHub

В приведенном ниже примере используются учетные данные пользовательского уровня, т.е. руководитель службы Azure для развертывания. Выполните действия по настройке секрета:

1. В [GitHub](https://github.com/)просмотрите репозиторий, выберите **Настройки > Секреты > Добавить новый секрет**

2. Вставьте содержимое ниже `az cli` команды как значение секретной переменной. Например, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Теперь в файле рабочего `.github/workflows/workflow.yml` процесса в вашей ветке: замените секрет в действии входа в Azure на ваш секрет.

4. Аналогичным образом, определите следующие дополнительные секреты для учетных данных реестра контейнеров и установите их в действие Docker login. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Вы видите секреты, как показано ниже, когда-то определено.

    ![секреты контейнера](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Создание изображения контейнера

В следующем примере показана часть рабочего процесса, которая создает изображение докера.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Развертывание в контейнере службы приложений

Чтобы развернуть изображение в пользовательском контейнере `azure/webapps-container-deploy@v1` в службе приложений, используйте действие. Это действие имеет пять параметров:

| **Параметр**  | **Объяснение**  |
|---------|---------|
| **имя приложения** | (Обязательно) Название приложения Службы Приложений | 
| **слот-имя** | (Необязательно) Введите существующий слот, кроме слота Production |
| **Изображения** | (Обязательно) Укажите имя полностью квалифицированного изображения контейнера(ы) Например, "myregistry.azurecr.io/nginx:latest" или "питон:3.7.2-альпийский/". Для приложения с несколькими контейнерами могут быть предоставлены несколько имен изображений контейнеров (многолинейные раздельные) |
| **конфигурация-файл** | (Необязательно) Путь файла Docker-Compose. Должен быть полностью квалифицированный путь или по отношению к рабочему каталогу по умолчанию. Требуется для многоконтейнерных приложений. |
| **контейнер-командование** | (Необязательно) Введите команду запуска. Для бывших. dotnet run или dotnet filename.dll |

Ниже приведен пример рабочего процесса для создания и развертывания приложения Node.js в пользовательском контейнере в Службе приложения.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти наш набор действий, сгруппированных в различные репозитории на GitHub, каждый из которых содержит документацию и примеры, чтобы помочь вам использовать GitHub для CI/CD и развернуть свои приложения в Azure.

- [Вход в Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp для контейнеров](https://github.com/Azure/webapps-container-deploy)

- [Докер войти / выход](https://github.com/Azure/docker-login)

- [События, запускающие рабочие процессы](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Развертывание K8](https://github.com/Azure/k8s-deploy)

- [Стартовые CI Рабочие процессы](https://github.com/actions/starter-workflows)

- [Стартовые рабочие процессы для развертывания в Azure](https://github.com/Azure/actions-workflow-samples)
