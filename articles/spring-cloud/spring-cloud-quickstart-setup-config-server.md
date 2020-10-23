---
title: Краткое руководство. Настройка сервера конфигурации Azure Spring Cloud
description: Узнайте, как настроить сервер конфигурации Azure Spring Cloud для развертывания приложений.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2f6051277f1ddb89e67ce8013c78571a2a7314b7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089134"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Краткое руководство. Настройка сервера конфигурации Azure Spring Cloud

Сервер конфигурации Azure Spring Cloud — это централизованная служба настройки для распределенных систем. Она использует уровень подключаемого репозитория, который сейчас поддерживает локальное хранилище, Git и Subversion. В этом кратком руководстве показано, как настроить сервер конфигурации для получения данных из репозитория Git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Предварительные требования

* Пройдите предыдущее краткое руководство из этой серии: [Подготовка службы Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Процедуры настройки сервера конфигурации Azure Spring Cloud

Настройте файл config-server, указав расположение репозитория Git для проекта с помощью следующей команды: Замените `<service instance name>` именем службы, созданной ранее. Значение по умолчанию для имени экземпляра службы, заданное в предыдущем кратком руководстве, не работает с этой командой.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Эта команда указывает серверу конфигурации найти данные конфигурации в папке [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) репозитория примеров приложений. Так как имя приложения, которое будет получать данные конфигурации, это `planet-weather-provider`, используемым файлом будет [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Сервер конфигурации Azure Spring Cloud — это централизованная служба настройки для распределенных систем. Она использует уровень подключаемого репозитория, который сейчас поддерживает локальное хранилище, Git и Subversion.  Настройте сервер конфигурации для развертывания приложений микрослужб в Azure Spring Cloud.

## <a name="prerequisites"></a>Предварительные требования

* [установите JDK версии 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (Необязательно.) [Установите Azure CLI версии 2.0.67 или более поздней](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
* (Необязательно.) [Установите Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) и [выполните вход](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

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
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

При прохождении этого краткого руководства вы создали ресурсы Azure, за которые будет взиматься плата, если вы оставите их в своей подписке. Если вы не собираетесь работать со следующим кратким руководством, перейдите к разделу [Очистка ресурсов](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Следующее краткое руководство:

> [!div class="nextstepaction"]
> [Сборка и развертывание приложений](spring-cloud-quickstart-deploy-apps.md)