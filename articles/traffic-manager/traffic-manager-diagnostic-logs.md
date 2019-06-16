---
title: Включение журнала ведения диагностики диспетчера трафика Azure
description: Сведения о включении журнала ведения диагностики для профиля диспетчера трафика и получения доступа к созданным в результате файлам журнала.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.openlocfilehash: b2ebeb41e69b7edfd43c38cc3b828069a1b3401a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071231"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Включение журнала ведения диагностики диспетчера трафика Azure

В этой статье описывается включение журнала ведения диагностики и получение доступа к данным журнала для профиля диспетчера трафика.

Журналы диагностики диспетчера трафика Azure могут помочь понять поведение ресурса профиля диспетчера трафика. Например, данные журнала профиля можно использовать, чтобы определить, почему для конечной точки истекло время ожидания отдельных проб.

## <a name="enable-diagnostic-logging"></a>Включение ведения журналов диагностики

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете выполнить приведенные ниже команды в [Azure Cloud Shell](https://shell.azure.com/powershell) или с помощью PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Если вы решили запустить PowerShell на своем компьютере, вам потребуется модуль Azure PowerShell 1.0.0 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы войти в Azure.

1. **Извлечение профиля диспетчера трафика.**

    Чтобы включить журнал ведения диагностики, требуется идентификатор профиля диспетчера трафика. Извлеките профиль диспетчера трафика, для которого необходимо включить журнал ведения диагностики, используя командлет [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Выходные данные содержат сведения об идентификаторе профиля диспетчера трафика.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Включение журнала ведения диагностики для профиля диспетчера трафика.**

    Включите журнал ведения диагностики для профиля диспетчера трафика с помощью идентификатора, полученного на предыдущем шаге, используя командлет [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Следующая команда позволяет сохранить журналы подробных сведений профиля диспетчера трафика для указанной учетной записи службы хранилища Azure. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Проверка параметров диагностики.**

      Проверьте параметры диагностики для профиля диспетчера трафика с помощью командлета [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Следующая команда позволяет просмотреть категории, зарегистрированные для ресурса.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Убедитесь, что все категории журнала, связанные с ресурсом профиля диспетчера трафика, отображаются как включенные. Проверьте также, правильно ли настроена учетная запись хранения.

## <a name="access-log-files"></a>Файлы журнала доступа
1. Войдите на [портале Azure](https://portal.azure.com). 
1. Войдите в свою учетную запись службы хранилища Azure на портале.
2. На странице **Обзор** учетной записи хранения Azure в разделе **Службы** выберите **Большие двоичные объекты**.
3. Для **контейнеров**выберите **insights-logs-probehealthstatusevents**, перейдите вниз к файлу PT1H.json и щелкните **Загрузка**, чтобы скачать и сохранить копию этого файла журнала.

    ![Получение доступа к файлам журнала профиля диспетчера трафика из хранилища BLOB-объектов](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Схема журнала диспетчера трафика

Все журналы диагностики, доступные в Azure Monitor, предоставляют гибкую общую схему верхнего уровня, позволяющую каждой службе задавать уникальные свойства для своих событий. Дополнительные сведения о схемах журналов диагностики верхнего уровня см. в статье [Supported services, schemas, and categories for Azure Diagnostic Logs](../azure-monitor/platform/tutorial-dashboards.md) (Поддерживаемые службы, схемы и категории для журналов диагностики Azure).

В следующей таблице приведены схемы журналов, относящиеся к ресурсу профиля диспетчера трафика Azure.

|||||
|----|----|---|---|
|**Имя поля**|**Тип поля**|**Определение**|**Пример**|
|EndpointName|String|Имя конечной точки диспетчера трафика, для которой выполняется запись состояния работоспособности.|*myPrimaryEndpoint*|
|Status|String|Состояние работоспособности проверенной конечной точки диспетчера трафика. Состояние может иметь значение **Работает** или **Не работает**.|**Работает**.|
|||||

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в статье [Мониторинг конечных точек в диспетчере трафика](traffic-manager-monitoring.md).

