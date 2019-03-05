---
title: Развертывание в Функциях Azure с помощью подключаемого модуля Jenkins для Функций Azure
description: Из этой статьи вы узнаете, как выполнять развертывание в Функциях Azure с помощью подключаемого модуля Jenkins для Функций Azure.
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 1138af0e073f68842861df86acd4d9d6eb467782
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824715"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Развертывание в Функциях Azure с помощью подключаемого модуля Jenkins для Функций Azure

[Функции Azure](/azure/azure-functions/) — это независимая от сервера служба вычислений. С помощью Функций Azure вы можете выполнить код по требованию, не запуская операции по подготовке инфраструктуры или управлению ею. В этом руководстве рассматривается, как развернуть функцию Java в Функциях Azure с помощью подключаемого модуля для Функций Azure.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , прежде чем начинать работу.
- **Сервер Jenkins**. Если у вас не установлен сервер Jenkins, см. статью о [создании сервера Jenkins в Azure](./install-jenkins-solution-template.md).

 > [!TIP]
 > Используемый в этом руководстве исходный код расположен в [репозитории GitHub для Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Создание функции Java

Чтобы создать функцию Java с помощью стека среды выполнения Java, используйте [портал Azure](https://portal.azure.com) или [Azure CLI](/cli/azure/?view=azure-cli-latest).

Ниже объясняется, как создать функцию Java с помощью Azure CLI:

1. Создайте группу ресурсов, заменив заполнитель **&lt;resource_group>** именем вашей группы ресурсов.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Создайте учетную запись хранения Azure, заменив заполнители соответствующими значениями.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Создайте приложение-функцию для тестирования, заменив заполнители соответствующими значениями.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Обновите среду выполнения до версии 2.x, заменив заполнители соответствующими значениями.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

Ниже описывается, как подготовить сервер Jenkins:

1. Разверните [сервер Jenkins](https://aka.ms/jenkins-on-azure) в Azure. Если у вас еще не установлен экземпляр сервера Jenkins, в статье [Создание сервера Jenkins на виртуальной машине Azure под управлением Linux на портале Azure](./install-jenkins-solution-template.md) приведены соответствующие пошаговые инструкции.

1. Войдите в экземпляр Jenkins по SSH.

1. Установите на экземпляре Jenkins средство Maven с помощью следующей команды:

    ```terminal
    sudo apt install -y maven
    ```

1. Установите [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) на экземпляре Jenkins. Для этого введите следующие команды в строке терминала:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Установите следующие подключаемые модули на панели мониторинга Jenkins:

    - подключаемый модуль для Функций Azure;
    - подключаемый модуль EnvInject.

1. Серверу Jenkins требуется субъект-служба Azure для аутентификации и доступа к ресурсам Azure. Пошаговые инструкции см. в статье [Руководство. Развертывание из GitHub в Службе приложений Azure с использованием непрерывной интеграции и непрерывного развертывания Jenkins](./tutorial-jenkins-deploy-web-app-azure-app-service.md).

1. Используя субъект-службу Azure, добавьте на сервер Jenkins тип учетных данных "Microsoft Azure Service Principal" (Субъект-служба Microsoft Azure). Дополнительные сведения см. в [указанной выше статье](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="create-a-jenkins-pipeline"></a>Создание конвейера Jenkins

В этом разделе описано, как создать [конвейер Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Создайте конвейер на панели мониторинга Jenkins.

1. Установите флажок **Prepare an environment for the run** (Подготовить среду к запуску).

1. Добавьте следующие переменные среды в поле **Properties Content** (Содержимое свойств), заменив заполнители соответствующими значениями для своей среды:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. В разделе **Pipeline->Definition** (Конвейер->Определение) выберите **Pipeline script from SCM** (Скрипт конвейера из SCM).

1. Введите URL-адрес репозитория SCM и путь к скрипту, используя приведенный [скрипт в качестве примера](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

## <a name="build-and-deploy"></a>Сборка и развертывание

Теперь можно выполнить задание Jenkins.

1. Сначала получите ключ авторизации, выполнив инструкции из статьи [Триггеры и привязки HTTP в службе "Функции Azure"](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys).

1. Введите URL-адрес приложения в окне браузера. Замените заполнители соответствующими значениями и укажите числовое значение для заполнителя **&lt;input_number>** в качестве ввода для функции Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Отобразятся результаты, аналогичные приведенным ниже в качестве примера выходным данным (для тестирования использовалось нечетное число 365):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданные ресурсы, выполнив следующее действие:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Функциях Azure:
> [!div class="nextstepaction"]
> [Документация по Функциям Azure](/azure/azure-functions/)