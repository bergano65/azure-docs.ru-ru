---
title: Краткое руководство. Настройка сервера конфигурации Azure Spring Cloud
description: Узнайте, как настроить сервер конфигурации Azure Spring Cloud для развертывания приложений.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951665"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Краткое руководство. Настройка сервера конфигурации Azure Spring Cloud

Сервер конфигурации Azure Spring Cloud — это централизованная служба настройки для распределенных систем. Она использует уровень подключаемого репозитория, который сейчас поддерживает локальное хранилище, Git и Subversion.  Настройте сервер конфигурации для развертывания приложений микрослужб в Azure Spring Cloud.

## <a name="prerequisites"></a>Предварительные требования

* [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (Необязательно.) [Установите Azure CLI версии 2.0.67 или более поздней](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
* (Необязательно.) [Установите Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) и [выполните вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="azure-spring-cloud-config-server-procedures"></a>Процедуры настройки сервера конфигурации Azure Spring Cloud

#### <a name="portal"></a>[Портал](#tab/Azure-portal)

Следующая процедура настраивает сервер конфигурации с помощью портала Azure для развертывания [примера Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Перейдите на страницу **Обзор** службы и выберите **Config Server** (Сервер конфигурации).

2. В разделе **Репозиторий по умолчанию** задайте для **URI** значение "https://github.com/Azure-Samples/piggymetrics-config".

3. Щелкните **Применить**, чтобы сохранить изменения.

    ![Снимок экрана, где отображается окно портала ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Следующая процедура настраивает сервер конфигурации с помощью Azure CLI для развертывания [примера Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Настройте файл config-server, указав расположение репозитория Git для проекта:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Сборка и развертывание приложений](spring-cloud-quickstart-deploy-apps.md)
