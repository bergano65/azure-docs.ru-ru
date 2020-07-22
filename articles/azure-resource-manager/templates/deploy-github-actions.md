---
title: Развертывание шаблонов Resource Manager с помощью действий GitHub
description: Описывает развертывание шаблонов Azure Resource Manager с помощью действий GitHub.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854746"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Развертывание шаблонов Resource Manager с помощью действий GitHub

Функция [GitHub Actions](https://help.github.com/en/actions) позволяет создавать пользовательские рабочие процессы для жизненного цикла разработки программного обеспечения непосредственно в репозитории GitHub, где хранятся шаблоны Azure Resource Manager (ARM). [Рабочий процесс](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) определяется файлом YAML. Рабочие процессы включают одно или несколько заданий, и каждое задание содержит набор действий, выполняющих отдельные задачи. Шаги могут выполнять команды или использовать действие. Вы можете создавать собственные действия или использовать действия, общие для [сообщества GitHub](https://github.com/marketplace?type=actions), и настраивать их по мере необходимости. В этой статье показано, как использовать [действия Azure CLI](https://github.com/marketplace/actions/azure-cli-action) для развертывания шаблонов Resource Manager.

Действие Azure CLI имеет два зависимых действия.

- **[Извлечение](https://github.com/marketplace/actions/checkout)** : извлечение репозитория, чтобы рабочий процесс мог получить доступ к любому указанному шаблону Resource Manager.
- **[Вход в Azure](https://github.com/marketplace/actions/azure-login)** : вход с помощью учетных данных Azure.

Базовый рабочий процесс развертывания шаблона Resource Manager может содержать три шага.

1. Извлечение файла шаблона.
2. Войдите в Azure.
3. Развертывание шаблона Resource Manager

## <a name="prerequisites"></a>Предварительные требования

Для хранения шаблонов Resource Manager и файлов рабочих процессов необходим репозиторий GitHub. Чтобы создать его, см. раздел [Создание нового репозитория](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Настройка учетных данных развертывания

Действие входа в Azure использует субъект-службу для проверки подлинности в Azure. Для развертывания ресурсов Azure субъекту рабочего процесса CI/CD обычно требуется встроенный участник.

В следующем сценарии Azure CLI показано, как создать субъект-службу Azure с разрешениями участника в группе ресурсов Azure. В этой группе ресурсов рабочий процесс развертывает ресурсы, определенные в шаблоне Resource Manager.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Настройте значения **$projectName** и **$location** в сценарии. Имя группы ресурсов — это имя проекта с добавлением **rg**. В рабочем процессе нужно указать имя группы ресурсов и имя зоны.

Скрипт выводит объект JSON, подобный следующему:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Скопируйте выходные данные JSON и сохраните их в качестве секрета GitHub в репозитории GitHub. Если у вас еще нет репозитория, см. [Предварительные требования](#prerequisites).

1. В репозитории GitHub выберите вкладку **Параметры**.
1. Выберите **Секреты** в левом меню.
1. Введите следующие значения.

    - **Name** (Имя). AZURE_CREDENTIALS
    - **Value** (Значение): (вставьте выходные данные JSON).
1. Выберите **Добавить секрет**.

В рабочем процессе необходимо указать имя секрета.

## <a name="add-resource-manager-template"></a>Добавление шаблона Resource Manager

Добавьте шаблон Resource Manager в репозиторий GitHub. Если у вас нет своего шаблона, можно использовать следующий шаблон. Шаблон создает учетную запись хранения.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Файл можно разместить в любом месте репозитория. Пример рабочего процесса в следующем разделе предполагает, что файл шаблона имеет имя **azuredeploy.json** и хранится в папке с именем **templates** в корне репозитория.

## <a name="create-workflow"></a>Создание рабочего процесса

Файл рабочего процесса должен храниться в папке **. GitHub/Workflows** в корне репозитория. Расширение файла рабочего процесса может быть либо **.yml**, либо **.yaml**.

Вы можете создать файл рабочего процесса и отправить его в репозиторий либо использовать следующую процедуру:

1. В репозитории GitHub выберите **Действия** в верхнем меню.
1. Выберите **Новый рабочий процесс**.
1. Выберите **настроить рабочий процесс вручную**.
1. Переименуйте файл рабочего процесса, если вы предпочитаете другое имя, отличное от **main.yml**. Например: **deployStorageAccount.yml**.
1. Замените содержимое YML-файла приведенным ниже:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Файл рабочего процесса содержит три раздела.

    - **name**. Имя рабочего процесса.
    - **on**. Имя событий GitHub, которые инициируют рабочий процесс. Рабочий процесс инициируется при наличии события отправки в главной ветви, которое изменяет по крайней мере один из двух указанных файлов. Два файла — это файл рабочего процесса и файл шаблона.

        > [!IMPORTANT]
        > Убедитесь, что эти два файла и их пути совпадают с вашими.
    - **jobs**. Выполнение рабочего процесса состоит из одного или нескольких заданий. Тут есть только одно задание, которое называется **deploy-storage-account-template**.  Это задание включает три шага:

        - **Извлечение исходного кода**.
        - **Вход в Azure**.

            > [!IMPORTANT]
            > Убедитесь, что имя секрета соответствует сохраненному в репозитории. См. раздел [Настройка учетных данных развертывания](#configure-deployment-credentials).
        - **Развертывание шаблона ARM**. Замените значение **resourceGroupName**.  Если вы использовали сценарий Azure CLI в [Настройка учетных данных развертывания](#configure-deployment-credentials), то имя создаваемой группы ресурсов — это имя проекта с добавлением **rg**. Проверьте значение **templateLocation**.

1. Выберите **Начать фиксацию**.
1. Выберите **Зафиксировать непосредственно в главной ветви**.
1. Выберите **Зафиксировать новый файл** (или **Зафиксировать изменения**).

Так как рабочий процесс настроен на активацию файлом рабочего процесса или обновлением файла шаблона, рабочий процесс запускается сразу после фиксации изменений.

## <a name="check-workflow-status"></a>Проверка состояния рабочего процесса

1. Выберите вкладку **Действия**. Вы увидите **Create deployStorageAccount.yml** в списке. Выполнение рабочего процесса занимает 1–2 минуты.
1. Выберите рабочий процесс, чтобы открыть его.
1. В меню слева выберите **deploy-storage-account-template** (имя задания). Имя задания определено в рабочем процессе.
1. Выберите **Развернуть шаблон ARM** (имя шага), чтобы раскрыть его. Вы видите ответ REST API.

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по созданию шаблона см. в статье [Учебник. Создание и развертывание первого шаблона ARM](template-tutorial-create-first-template.md).
