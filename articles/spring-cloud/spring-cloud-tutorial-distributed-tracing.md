---
title: Учебник. Использование распределенной трассировки с помощью Azure Spring Cloud
description: В этом учебнике показано, как использовать распределенную трассировку в Spring Cloud с помощью Azure Application Insights.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708760"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Руководство по использованию распределенной трассировки с помощью Azure Spring Cloud

Инструменты распределенной трассировки в Spring Cloud позволяют легко выполнять отладку и мониторинг сложных проблем. Azure Spring Cloud интегрирует [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) с [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview), чтобы вы получили мощную возможность распределенной трассировки на портале Azure.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Включение распределенной трассировки на портале Azure.
> * Добавление Spring Cloud Sleuth в приложение.
> * Просмотр сопоставления зависимостей для приложений для микрослужб.
> * Поиск данных трассировки с помощью различных фильтров.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* Подготовленная и запущенная служба Azure Spring Cloud.  Выполните инструкции, приведенные в [этом кратком руководстве](spring-cloud-quickstart-launch-app-cli.md), чтобы подготовить и запустить службу Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Добавление зависимостей

Включите отправителя zipkin для отправки в Интернет, добавив в файл application.properties следующую строку:

```xml
spring.zipkin.sender.type = web
```

Вы можете пропустить следующий шаг, если уже ознакомились с [учебником по подготовке приложения Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). В противном случае перейдите в локальную среду разработки и измените файл `pom.xml`, чтобы включить зависимость Spring Cloud Sleuth:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth</artifactId>
            <version>${spring-cloud-sleuth.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
</dependencies>
```

* Повторите сборку и развертывание для службы Azure Spring Cloud, чтобы отразить эти изменения. 

## <a name="modify-the-sample-rate"></a>Изменение частоты выборки
Частоту сбора данных телеметрии можно изменить, модифицируя частоту выборки. Например, если вы хотите выполнять выборку вдвое реже, перейдите к файлу `application.properties` и измените следующую строку:

```xml
spring.sleuth.sampler.probability=0.5
```

Если вы уже создали и развернули приложение, можно изменить частоту выборки, добавив указанную выше строку в качестве переменной среды в Azure CLI или на портале. 

## <a name="enable-application-insights"></a>Включение Application Insights

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure.
1. В разделе "Мониторинг" выберите **Distributed Tracing** (Распределенная трассировка).
1. Выберите **Изменить параметр**, чтобы изменить или добавить новый параметр.
1. Создайте запрос Application Insights или выберите имеющийся.
1. Выберите категорию ведения журнала, которую требуется отслеживать, и укажите период удержания (в днях).
1. Выберите **Применить**, чтобы применить новую трассировку.

## <a name="view-application-map"></a>Просмотр схемы приложения

Вернитесь на страницу Distributed Tracing (Распределенная трассировка) и выберите **View application map** (Просмотреть схему приложений). Проверьте визуальное представление приложения и параметры мониторинга. Сведения об использовании схемы приложения см. в [этой статье](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Поиск

Используйте функцию поиска для запроса других конкретных элементов телеметрии. На странице **Distributed Tracing** (Распределенная трассировка) выберите **Поиск**. Дополнительные сведения об использовании функции поиска см. в [этой статье](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Страница Application Insights

Кроме поиска и схемы приложений, Application Insights предоставляет возможности мониторинга. Найдите на портале Azure имя своего приложения, а затем откройте страницу Application Insights, чтобы узнать больше. Дополнительные сведения об использовании этих инструментов [см. в документации](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Отключение Application Insights

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure.
1. В разделе "Мониторинг" щелкните **Distributed Tracing** (Распределенная трассировка).
1. Щелкните **Отключить**, чтобы отключить Application Insights

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы узнали, как включить и изучить распределенную трассировку в Azure Spring Cloud. Чтобы узнать, как привязать приложение к Azure CosmosDB, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Узнайте, как привязать приложение к Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
