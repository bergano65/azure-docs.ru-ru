---
title: Краткое руководство. Подготовка службы Azure Spring Cloud
description: Узнайте, как создать экземпляр службы Azure Spring Cloud для развертывания приложений.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951676"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Краткое руководство. Подготовка службы Azure Spring Cloud

Вы можете создать экземпляр Azure Spring Cloud с помощью портала Azure или Azure CLI.  Оба метода описаны в следующих процедурах.
## <a name="prerequisites"></a>Предварительные требования

* [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (Необязательно.) [Установите Azure CLI версии 2.0.67 или более поздней](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
* (Необязательно.) [Установите Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) и [выполните вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Подготовка к работе экземпляра Azure Spring Cloud

#### <a name="portal"></a>[Портал](#tab/Azure-portal)

Следующая процедура создает экземпляр Azure Spring Cloud с помощью портала Azure.

1. На новой вкладке откройте [портал Azure](https://ms.portal.azure.com/). 

2. В поле поиска сверху введите **Azure Spring Cloud**.

3. Выберите пункт **Azure Spring Cloud** в списке результатов.

    ![Значок ASC — запуск](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. На странице Azure Spring Cloud щелкните элемент **+ Добавить**.

    ![Значок ASC — добавление](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Заполните форму на странице **создания** Azure Spring Cloud.  Ознакомьтесь со следующими рекомендациями:
    - **Подписка**: Выберите подписку, на которую будет выставляться счет за этот ресурс.
    - **Группа ресурсов.** Советуем создавать группы ресурсов для новых ресурсов. Обратите внимание, что она будет использоваться на следующих шагах как **\<resource group name\>** .
    - **Сведения о службе или ее название.** Укажите **\<service instance name\>** .  Его длина должна быть от 4 до 32 знаков. Имя может содержать только строчные буквы, цифры и дефисы.  Первым символом в имени службы должна быть буква, а последним — буква или цифра.
    - **Расположение.** Выберите расположение для экземпляра службы.

    ![Начальная страница портала ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Щелкните **Проверка и создание**.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Следующая процедура подготавливает экземпляр Azure Spring Cloud с помощью расширения Azure CLI.

1. Войдите в Azure CLI и выберите активную подписку. Выбранная активная подписка должна быть добавлена в список разрешений для Azure Spring Cloud.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Подготовьте имя службы Azure Spring Cloud.  Его длина должна быть от 4 до 32 знаков. Имя может содержать только строчные буквы, цифры и дефисы.  Первым символом в имени службы должна быть буква, а последним — буква или цифра.

1. Создайте группу ресурсов, которая будет содержать службу Azure Spring Cloud.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Узнайте больше о [группах ресурсов Azure](../azure-resource-manager/management/overview.md).

1. Откройте окно Azure CLI и выполните приведенные ниже команды, чтобы подготавливать экземпляр Azure Spring Cloud к работе.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Развертывание экземпляра службы займет около пяти минут.
---

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Настройка сервера конфигурации](spring-cloud-quickstart-setup-config-server.md)


