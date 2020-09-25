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
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885707"
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

* [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (Необязательно.) [Установите Azure CLI версии 2.0.67 или более поздней](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
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
::: zone-end

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующему краткому руководству из этой серии, пропустите этот шаг.

В этих кратких руководствах показано, как создать ресурсы Azure, за которые будет взиматься плата, если они останутся в вашей подписке. Если вы не планируете продолжать работу со следующим кратким руководством или работать с этими ресурсами в будущем, вы можете удалить группу ресурсов с помощью портала, или выполнив следующую команду в Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

В предыдущем кратком руководстве также показано, как задать имя группы ресурсов по умолчанию. Если вы не планируете перейти к следующему краткому руководству, очистите это значение по умолчанию, выполнив такую команду CLI:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сборка и развертывание приложений](spring-cloud-quickstart-deploy-apps.md)
