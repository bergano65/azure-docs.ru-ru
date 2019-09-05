---
title: Общие сведения о пользовательском интерфейсе Azure IoT Central | Документация Майкрософт
description: Ознакомьтесь с ключевыми областями пользовательского интерфейса Azure IoT Central, с помощью которого сборщики создают решения IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4a0c9d16474ddf032ff88382bc240713bc734ff8
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211898"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Общие сведения о пользовательском интерфейсе Azure IoT Central (предварительные версии функций)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

В этой статье содержатся сведения о пользовательском интерфейсе Microsoft Azure IoT Central. С помощью пользовательского интерфейса можно создавать и использовать решение Azure IoT Central и их подключенные устройства, а также управлять ими.

С помощью пользовательского интерфейса Azure IoT Central _сборщики_  могут определить свое решение Azure IoT Central. Этот интерфейс позволяет выполнять следующие задачи:

* определение типов устройств, подключающихся к решению.
* настройка правил и действий устройств;
* настройка пользовательского интерфейса для _операторов_, использующих решение.

С помощью пользовательского интерфейса Azure IoT Central _оператор_ может управлять решением Azure IoT Central. Этот интерфейс позволяет выполнять следующие задачи:

* мониторинг устройств;
* настройка устройств;
* устранение проблем и неполадок с устройствами;
* подготавливать новые устройства;

## <a name="use-the-left-navigation-menu"></a>Использование меню навигации слева

Меню навигации слева позволяет получать доступ к различным разделам приложения. Меню навигации можно развернуть или свернуть, выбрав **<** или **>** .

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Devices** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device groups** lets you view and create device groups. As an operator, you can create device groups as a logical collections of devices specified by a query.

      **Rules** lets you edit rules that fire based on device telemetry and trigger customizable actions.
    
      **Analytics** shows analytics derived from device telemetry for devices and device groups. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to update your devices at scale.
    
      **Device templates** shows the tools a builder uses to create and manage device templates.
    
      **Data export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Поиск, справка и поддержка

На каждой странице сверху отображается меню.

![Панель инструментов](media/overview-iot-central-tour-pnp/toolbar.png)

* Чтобы найти устройства и шаблоны устройств, введите значение в **Поиск**.
* Чтобы изменить язык пользовательского интерфейса или тему, щелкните значок **Параметры**.
* Чтобы выйти из приложения, щелкните значок **Учетная запись**.
* Чтобы получить справку и поддержку, выберите раскрывающийся список **Справка** с перечнем ресурсов. В пробной версии приложения ресурсы поддержки включают в себя доступ к [чату в реальном времени](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Вы можете выбирать между светлой или темной темой пользовательского интерфейса.

![Выбор темы пользовательского интерфейса](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> Возможность выбора между светлой и темной темами будет недоступен, если администратор настроил пользовательскую тему для приложения.

## <a name="dashboard"></a>панель мониторинга

![панель мониторинга](media/overview-iot-central-tour-pnp/homepage.png)

* Панель мониторинга — это первая страница, отображаемая при входе в приложение Azure IoT Central. Как сборщик вы можете настроить панель мониторинга приложения для других пользователей путем добавления плиток. Дополнительные сведения см. в статье учебника [по настройке шаблона устройства](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

* Оператор может создать персонализированные панели мониторинга и переключаться между ними и панелью мониторинга по умолчанию. Дополнительные сведения см. в статье [Создание и управление ими персональные информационные панели](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="devices"></a>Устройства

![Страница "Устройства"](media/overview-iot-central-tour-pnp/explorer.png)

На странице обозревателя отображаются _устройства_ в приложении Azure IoT Central, сгруппированные по _шаблону устройства_.

* Шаблон определяет тип устройства, которое можно подключить к приложению. Дополнительные сведения см. в статье [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Определение типа нового устройства в приложении Azure IoT Central).
* В устройстве могут использоваться два типа приложений: реальные и смоделированные. Дополнительные сведения см. в статье [Add a real device to your Azure IoT Central application](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Добавление нового устройства в приложение Azure IoT Central).

## <a name="device-groups"></a>Группы устройств

![Страница "Группы устройств"](media/overview-iot-central-tour-pnp/devicesets.png)

На странице _Группы устройств_ отображаются группы, созданные сборщиком. Группа устройств — это коллекция связанных устройств. Сборщик определяет запрос для идентификации устройств, входящих в группу. Группы устройств используются при настройке аналитики в приложении. Дополнительные сведения см. в статье [Use device groups in your Azure IoT Central application (preview features)](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Использование групп устройств в приложении Azure IoT Central (предварительные версии функций)).

## <a name="rules"></a>Правила

![Страница "Правила"](media/overview-iot-central-tour-pnp/rules.png)

На станице "Правила" можно определить правила на основе телеметрии, состояния или событий устройства. При срабатывании правила может активироваться действие, например отправка сообщения оператору. С помощью этой страницы сборщик создает правила и управляет ими. Дополнительные сведения см. в статье [Tutorial: Configure rules and actions for your device in Azure IoT Central (preview features)](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Учебник. Настройка правил и действий для устройства в Azure IoT Central (предварительные версии функций)).

## <a name="analytics"></a>Analytics

![Страница аналитики](media/overview-iot-central-tour-pnp/analytics.png)

На странице аналитики отображаются диаграммы, с помощью которых можно определить поведение устройств, подключенных к приложению. На ней оператор может отслеживать и анализировать проблемы с подключенными устройствами. Сборщик может определять диаграммы, отображаемые на этой странице. Дополнительные сведения см. в статье [How to use analytics to analyze your device data](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Анализ данных устройства с помощью аналитики).

## <a name="jobs"></a>Задания

![Страница "Задания"](media/overview-iot-central-tour-pnp/jobs.png)

На странице "Задания" можно выполнять массовые операции управления устройствами. Построитель использует эту страницу для обновления свойств, параметров и команд устройства. Чтобы узнать больше, ознакомьтесь со статьей [Создание и запуск заданий в приложении Azure IoT Central](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Шаблоны устройств

![Страница шаблонов устройств](media/overview-iot-central-tour-pnp/templates.png)

На странице шаблонов устройств построитель создает шаблоны устройств в приложении и управляет ими. Шаблон устройства определяет характеристики устройства, в том числе:

* данные телеметрии, состояние и измерения событий;
* свойства;
* команды;

Сборщик также может создавать формы и панели мониторинга, которые операторы могут использовать для управления устройствами.

Дополнительные сведения см. в статье [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Определение типа нового устройства в приложении Azure IoT Central).

## <a name="data-export"></a>Экспорт данных

![Страница "Экспорт данных"](media/overview-iot-central-tour-pnp/export.png)

На странице "Экспорт данных" администратор определяет, как выполнять потоковую передачу данных, например данных телеметрии, из приложения. Другие сервисы могут хранить экспортированные данные или использовать их для анализа. Дополнительные сведения см. в статье [Экспорт данных в Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="administration"></a>Администрирование

![Страница администрирования](media/overview-iot-central-tour-pnp/administration.png)

Страница "Администрирование" содержит ссылки на инструменты, используемые администратором, такие как определение пользователей и ролей в приложении, а также настройка пользовательского интерфейса. Дополнительные сведения см. в статье [How to administer your application](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Как управлять приложением).

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы ознакомились с общими сведениями об Azure IoT Central и макетом пользовательского интерфейса, рекомендуем приступить к изучению краткого руководства [Create an Azure IoT Central application](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Создание приложения Azure IoT Central).
