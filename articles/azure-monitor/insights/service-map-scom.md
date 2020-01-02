---
title: Интеграция Azure Monitor для виртуальных машин Map с Operations Manager | Документация Майкрософт
description: Azure Monitor для виртуальных машин автоматически обнаруживает компоненты приложений в системах Windows и Linux и сопоставляет взаимодействие между службами. В этой статье рассматривается использование функции Map для автоматического создания схем распределенного приложения в Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: f1acf3c1574fd94606d75c6250dedd40a9c7ea4d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849825"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Интеграция System Center Operations Manager с функцией карт Azure Monitor для виртуальных машин

В Azure Monitor для виртуальных машин можно просматривать обнаруженные компоненты приложения на виртуальных машинах Windows и Linux, работающих в Azure или в среде. Благодаря такой интеграции между функцией Map и System Center Operations Manager можно автоматически создавать схемы распределенных приложений в Operations Manager, основанные на динамических картах зависимостей в Azure Monitor для виртуальных машин. В этой статье описывается настройка группы управления System Center Operations Manager для поддержки этой функции.

>[!NOTE]
>Если вы уже развернули Сопоставление служб, вы можете просматривать карты в Azure Monitor для виртуальных машин, включая дополнительные функции для мониторинга работоспособности и производительности виртуальных машин. Функция Map Azure Monitor для виртуальных машин предназначена для замены автономного Сопоставление службного решения. Дополнительные сведения см. в разделе [Общие сведения об Azure Monitor для виртуальных машин](vminsights-overview.md).

## <a name="prerequisites"></a>Технические условия

* Группа управления System Center Operations Manager (2012 R2 или более поздней версии).
* Log Analytics рабочей области, настроенной для поддержки Azure Monitor для виртуальных машин.
* Одна или несколько виртуальных машин Windows и Linux или физических компьютеров, которые отслеживаются Operations Manager и отправляют данные в рабочую область Log Analytics. Серверы Linux, передающие данные группе управления Operations Manager, необходимо настроить для прямого подключения к Azure Monitor. Дополнительные сведения см. в обзоре статьи [Получение данных журнала с помощью агента log Analytics](../platform/log-analytics-agent.md).
* Субъект-служба с доступом к подписке Azure, связанной с рабочей областью Log Analytics. Дополнительные сведения см. в руководстве по [созданию субъекта-службы](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Установка пакета управления схемы услуги

Интеграция между Operations Manager и функцией Map включается путем импорта пакета управления Microsoft. SystemCenter. ServiceMap (Microsoft. SystemCenter. ServiceMap. MPB). Скачать пакет управления можно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=55763). Этот набор содержит следующие пакеты управления:

* Microsoft.ServiceMap.Application.Views;
* Microsoft.System.Center.ServiceMap.Internal;
* Microsoft.System.Center.ServiceMap.Overrides;
* Microsoft.System.Center.ServiceMap.

## <a name="configure-integration"></a>Настройка интеграции

После установки пакета управления Сопоставление служб в разделе **Operations Management Suite** в области **администрирование** консоли управления Operations Manager появится новый узел **сопоставление служб**.

>[!NOTE]
>[Operations Management Suite был набор служб](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) , включающих log Analytics, теперь является частью [Azure Monitor](../overview.md).

Чтобы настроить интеграцию с картой Azure Monitor для виртуальных машин, выполните следующие действия.

1. Чтобы открыть мастер настройки, щелкните **Добавить рабочую область** в области **Service Map Overview** (Обзор схемы услуги).  

    ![Область "Service Map Overview" (Обзор схемы услуги)](media/service-map-scom/scom-configuration.png)

2. В окне **Настройка подключения** введите имя или идентификатор клиента, идентификатор приложения (имя пользователя или clientID) и пароль субъекта-службы, а затем нажмите кнопку **Далее**. Дополнительные сведения см. в руководстве по созданию субъекта-службы.

    ![Окно "Настройка подключения"](media/service-map-scom/scom-config-spn.png)

3. В окне **Subscription Selection** (Выбор подписки) выберите подписку Azure, группу ресурсов Azure (которая содержит рабочую область Log Analytics) и саму рабочую область Log Analytics, а затем нажмите кнопку **Далее**.

    ![Настройка Operations Manager: рабочая область](media/service-map-scom/scom-config-workspace.png)

4. В окне **Machine Group Selection** (Выбор группы компьютеров) выберите группы компьютеров решения "Сопоставление служб", которые необходимо синхронизировать с Operations Manager. Нажмите кнопку **Add/Remove Machine Groups** (Добавить или удалить группы компьютеров), выберите группы из списка **доступных групп компьютеров**и нажмите кнопку **Добавить**.  Выбрав группы, нажмите кнопку **ОК** для завершения.

    ![Настройка групп компьютеров в Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. В окне **Выбор сервера** Настройте группу серверов сопоставление служб с серверами, которые требуется синхронизировать между Operations Manager и функцией Map. Нажмите кнопку **Добавление и удаление серверов**.

    Для интеграции и успешного построения схемы распределенного приложения для сервера он должен:

   * Отслеживается Operations Manager
   * Настроено для передачи в рабочую область Log Analytics, настроенную с Azure Monitor для виртуальных машин
   * входить в группу серверов решения "Сопоставление служб".

     ![Настройка Operations Manager: группа](media/service-map-scom/scom-config-group.png)

6. Необязательно. Выберите пул ресурсов "все серверы управления" для взаимодействия с Log Analytics, а затем нажмите кнопку " **Добавить рабочую область**".

    ![Настройка Operations Manager: пул ресурсов](media/service-map-scom/scom-config-pool.png)

    Для настройки и регистрации рабочей области Log Analytics может потребоваться около минуты. После настройки Operations Manager инициирует первую синхронизацию карт.

    ![Настройка Operations Manager: пул ресурсов](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Мониторинг интеграции

После подключения рабочей области Log Analytics в области **мониторинг** консоли управления Operations Manager отображается новая папка сопоставление служб.

![Operations Manager: область "Мониторинг"](media/service-map-scom/scom-monitoring.png)

Папка "Сопоставление служб" имеет четыре узла:

* **Активные предупреждения**: содержит список всех активных оповещений о связи между Operations Manager и Azure Monitor.  

  >[!NOTE]
  >Эти предупреждения не Log Analytics предупреждения, синхронизированные с Operations Manager, они создаются в группе управления на основе рабочих процессов, определенных в пакете управления Сопоставление служб.

* **Серверы**: выводит список наблюдаемых серверов, для которых настроена синхронизация Azure Monitor для виртуальных машин функции карт.

    ![Operations Manager: область "Серверы"](media/service-map-scom/scom-monitoring-servers.png)

* **Представления зависимостей группы компьютеров**: перечисляет все группы компьютеров, синхронизированные из функции Map. Вы можете щелкнуть любую группу, чтобы просмотреть распределенные диаграммы приложения.

    ![Схема распределенного приложения Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Представления зависимостей сервера**: перечисляет все серверы, синхронизированные из функции Map. Вы можете щелкнуть любой сервер, чтобы просмотреть его схему распределенного приложения.

    ![Схема распределенного приложения Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Изменение или удаление рабочей области

Вы можете изменить или удалить настроенную рабочую область с помощью области **Service Map Overview** (Обзор схемы услуги) (область **Администрирование** > **Operations Management Suite** > **Схема услуги**).

>[!NOTE]
>[Набор Operations Management Suite представлял собой коллекцию служб](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand), среди которых была и служба Log Analytics. Теперь эта служба входит в состав [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

В текущем выпуске можно настроить только одну рабочую область Log Analytics.

![Operations Manager: область изменения рабочей области](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Настройка правил и переопределений

Правило *Microsoft. SystemCenter. ServiceMapImport. Rule*периодически извлекает сведения из функции карт Azure Monitor для виртуальных машин. Чтобы изменить интервал синхронизации, можно переопределить правило и изменить значение параметра **интервалминутес**.

![Operations Manager: окно свойств переопределений](media/service-map-scom/scom-overrides.png)

* **Enabled**: позволяет включить или отключить автоматическое обновление.
* **Интервалминутес**: указывает время между обновлениями. Значение по умолчанию — один час. Если вы хотите чаще синхронизировать карты, это значение можно изменить.
* **TimeoutSeconds**: указывает продолжительность времени до истечения времени ожидания запроса.
* **TimeWindowMinutes**: указывает временное окно для запроса данных. Значение по умолчанию — 60 минут, что является максимальным допустимым интервалом.

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения

В текущей версии существуют следующие проблемы и ограничения:

* Можно подключиться только к одной рабочей области Log Analytics.
* Хотя пользователи могут вручную добавить серверы в группу серверов решения "Сопоставление служб" с помощью области **Разработка**, сопоставления для этих серверов синхронизируются не сразу. Они будут синхронизированы из функции Azure Monitor для виртуальных машин карт во время следующего цикла синхронизации.
* При внесении изменений в диаграммы распределенных приложений, созданные пакетом управления, эти изменения, скорее всего, будут перезаписаны при следующей синхронизации с Azure Monitor для виртуальных машин.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Официальная документация Azure по созданию субъекта-службы представлена в следующих статьях:

* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Обратная связь
У вас есть отзывы о интеграции с функцией Azure Monitor для виртуальных машин Map или этой документации? Посетите [страницу пользовательских мнений](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), где можно предложить функции или проголосовать за существующие предложения.
