---
title: Подключение журналов Office 365 к Azure Sentinel | Документация Майкрософт
description: Узнайте, как использовать соединитель журналов Office 365 для получения сведений о текущих действиях пользователя и администратора в Exchange и SharePoint, включая OneDrive.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559336"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Подключение журналов Office 365 к Azure Sentinel

Соединитель журналов [Office 365](https://docs.microsoft.com/office/) содержит сведения о Sentinel-метках Azure для текущих действий пользователя и администратора в **Exchange** и **SharePoint** (включая **OneDrive**). Эти сведения включают сведения о таких действиях, как скачивание файлов, запросы на доступ, изменения в событиях группы и операции с почтовыми ящиками, а также сведения о пользователе, выполнившего действия. Подключение журналов Office 365 к Azure Sentinel позволяет просматривать и анализировать эти данные в книгах, запрашивать их для создания пользовательских оповещений и внедрять их для улучшения процесса расследования, обеспечивая более подробную информацию о безопасности Office 365.

## <a name="prerequisites"></a>Предварительные условия

- У вас должны быть разрешения на чтение и запись в рабочей области Sentinel Azure.

- Необходимо иметь права глобального администратора или администратора безопасности на вашем арендаторе.

- Развертывание Office 365 должно находиться в том же клиенте, что и Рабочая область Azure Sentinel.

> [!IMPORTANT]
> - Чтобы соединитель мог получить доступ к данным через API-интерфейс действия управления Office 365, необходимо включить **единое ведение журнала аудита** в развертывании Office 365. В зависимости от типа лицензии Office 365/Microsoft 365 она может быть включена по умолчанию. Просмотрите [Центр безопасности и соответствия требованиям Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) , чтобы проверить состояние единого ведения журнала аудита в соответствии с типом лицензии.
> - Вы также можете вручную включить, отключить и проверить текущее состояние единого ведения журнала аудита Office 365. Инструкции см. в разделе [Включение или отключение поиска по журналам аудита Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Дополнительные сведения см. в [справочнике по API действия управления Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Как отмечалось выше, и как вы увидите на странице соединителя в разделе **типы данных**, соединитель Azure Sentinel Office 365 в настоящее время поддерживает прием журналов аудита только из Microsoft Exchange и SharePoint (включая OneDrive). Однако есть несколько внешних решений, если вы заинтересованы в переносе [данных из команд](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) или [других данных Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) в метку Azure. 

## <a name="enable-the-office-365-log-connector"></a>Включение соединителя журнала Office 365

1. В меню навигации меток Azure выберите **соединители данных**.

1. В списке **соединители данных** щелкните **Office 365**, а затем нажмите кнопку **Открыть соединительную страницу** в правом нижнем углу.

1. В разделе **Конфигурация**под названием установите флажки для журналов действий Office 365, которые вы хотите подключить к Azure Sentinel, и нажмите кнопку **Применить изменения**. 

   > [!NOTE]
   > Если вы ранее подключили несколько клиентов к Azure Sentinel, используя более раннюю версию соединителя Office 365, которая поддерживала эту возможность, вы сможете просматривать и изменять журналы, собранные из каждого клиента. Добавление дополнительных арендаторов не предусмотрено, но можно удалить ранее добавленные арендаторы.

1. Чтобы запросить данные журнала Office 365 в Log Analytics, введите `OfficeActivity` первую строку окна запроса.

## <a name="next-steps"></a>Дальнейшие действия
Этот документ описывает подключение Office 365 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступите к обнаружению угроз с помощью Azure Sentinel, используя [встроенные](tutorial-detect-threats-built-in.md) или [Настраиваемые](tutorial-detect-threats-custom.md) правила.

