---
title: Список надежных URL-адресов виртуальных рабочих столов Windows — Azure
description: Список URL-адресов, которые следует разблокировать, чтобы убедиться, что развертывание виртуальных рабочих столов Windows работает правильно.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3d19a60fd6a22eb9245722c6ff69d3b39c05d29e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023179"
---
# <a name="safe-url-list"></a>Список надежных URL-адресов

Вам потребуется разблокировать определенные URL-адреса, чтобы развертывание виртуальных рабочих столов Windows работало правильно. В этой статье перечислены эти URL-адреса, чтобы вы могли выяснить, какие из них являются надежными.

## <a name="virtual-machines"></a>Виртуальные машины

Виртуальные машины Azure, создаваемые для виртуальных рабочих столов Windows, должны иметь доступ к следующим URL-адресам в коммерческом облаке Azure:

|Адрес|Исходящий TCP-порт|Назначение|Тег службы|
|---|---|---|---|
|*.wvd.microsoft.com|443|Служба трафика|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Агент трафика|AzureCloud;|
|production.diagnostics.monitoring.core.windows.net|443|Агент трафика|AzureCloud;|
|* xt.blob.core.windows.net|443|Агент трафика|AzureCloud;|
|* eh.servicebus.windows.net|443|Агент трафика|AzureCloud;|
|* xt.table.core.windows.net|443|Агент трафика|AzureCloud;|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud;|
|kms.core.windows.net|1688;|Активация Windows|Интернет|
|mrsglobalsteus2prod.blob.core.windows.net|443|Обновления агента и стека SxS|AzureCloud;|
|wvdportalstorageblob.blob.core.windows.net|443|Поддержка портала Azure|AzureCloud;|
| 169.254.169.254 | 80 | [Конечная точка службы метаданных экземпляра Azure](../virtual-machines/windows/instance-metadata-service.md) | Н/Д |
| 168.63.129.16 | 80 | [Мониторинг работоспособности узла сеансов](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Н/Д |

>[!IMPORTANT]
>Виртуальный рабочий стол Windows теперь поддерживает тег полного доменного имени. Дополнительные сведения см. в статье [Использование Брандмауэра Azure для защиты развертываний виртуального рабочего стола Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Вместо URL-адресов мы рекомендуем использовать теги FQDN или теги службы, чтобы избежать проблем со службами. Указанные URL-адреса и теги соответствуют только сайтам и ресурсам Виртуальных рабочих столов Windows. Они не включают URL-адреса для других служб, например Azure Active Directory.

Виртуальные машины Azure, создаваемые для виртуальных рабочих столов Windows, должны иметь доступ к следующим URL-адресам в облаке Azure для государственных организаций:

|Адрес|Исходящий TCP-порт|Назначение|Тег службы|
|---|---|---|---|
|*. wvd.microsoft.us|443|Служба трафика|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Агент трафика|AzureCloud;|
|monitoring.core.usgovcloudapi.net|443|Агент трафика|AzureCloud;|
|fairfax.warmpath.usgovcloudapi.net;|443|Агент трафика|AzureCloud;|
|* xt.blob.core.usgovcloudapi.net|443|Агент трафика|AzureCloud;|
|*.servicebus.usgovcloudapi.net|443|Агент трафика|AzureCloud;|
|* xt.table.core.usgovcloudapi.net|443|Агент трафика|AzureCloud;|
|Kms.core.usgovcloudapi.net|1688;|Активация Windows|Интернет|
|mrsglobalstugviffx.core.usgovcloudapi.net|443|Обновления агента и стека SxS|AzureCloud;|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Поддержка портала Azure|AzureCloud;|
| 169.254.169.254 | 80 | [Конечная точка службы метаданных экземпляра Azure](../virtual-machines/windows/instance-metadata-service.md) | Н/Д |
| 168.63.129.16 | 80 | [Мониторинг работоспособности узла сеансов](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Н/Д |

В следующей таблице перечислены дополнительные URL-адреса, к которым виртуальные машины Azure могут иметь доступ:

|Адрес|Исходящий TCP-порт|Назначение|Azure gov|
|---|---|---|---|
|*.microsoftonline.com|443|Проверка подлинности в Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Служба телеметрии|None|
|www.msftconnecttest.com|443|Проверка, подключена ли ОС к Интернету.|None|
|*.prod.do.dsp.mp.microsoft.com|443|Центр обновления Windows|None|
|login.windows.net|443|Вход в Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Обновления для клиентского программного обеспечения OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Проверка отзыва сертификата|None|

>[!NOTE]
>В настоящее время виртуальный рабочий стол Windows не содержит список диапазонов IP-адресов, которые можно разблокировать, чтобы разрешить сетевой трафик. В настоящее время поддерживается только разблокирование конкретных URL-адресов.
>
>Список защищенных URL-адресов, связанных с Office, включая необходимые Azure Active Directory URL-адреса, см. в разделе [URL-адреса и диапазоны IP-адресов office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Для URL-адресов, включающих службу трафика, необходимо использовать подстановочный знак (*). Если вы предпочитаете не использовать "*" для трафика, связанного с агентом, URL-адреса без подстановочных знаков можно найти, выполнив приведенные ниже действия.
>
>1. Зарегистрируйте свои виртуальные машины в пуле узлов Виртуального рабочего стола Windows.
>2. Откройте **средство просмотра событий**, перейдите в **Журнал Windows**  >  **приложение**  >  **ВВД-Agent** и найдите событие с идентификатором 3701.
>3. Разблокируйте URL-адреса, найденные в событии с ИДЕНТИФИКАТОРом 3701. URL-адреса в событии с ИДЕНТИФИКАТОРом 3701 зависят от региона. Вам потребуется повторить процесс разблокировки с соответствующими URL-адресами для каждого региона, в котором вы хотите развернуть виртуальные машины.

## <a name="remote-desktop-clients"></a>Клиенты удаленного рабочего стола

Все используемые удаленный рабочий стол клиенты должны иметь доступ к следующим URL-адресам:

|Адрес|Исходящий TCP-порт|Назначение|Клиент(ы)|Azure gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Служба трафика|All|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Данные диагностики|All|*.servicebus.usgovcloudapi.net|
|сайт go.microsoft.com;|443|Microsoft FWLinks|All|None|
|aka.ms|443|Средство сокращения URL-адресов Майкрософт|All|None|
|docs.microsoft.com|443|Документация|All|None|
|privacy.microsoft.com|443|Заявление о конфиденциальности|All|None|
|query.prod.cms.rt.microsoft.com|443|Клиентские обновления|Классические приложения|None|

>[!IMPORTANT]
>Открытие этих URL-адресов является обязательным для надежной работы клиента. Блокировка доступа к этим URL-адресам не поддерживается и повлияет на функциональность службы.
>
>Эти URL-адреса соответствуют только клиентским сайтам и ресурсам. Этот список не содержит URL-адреса для других служб, например Azure Active Directory. Azure Active Directory URL-адреса можно найти по ИДЕНТИФИКАТОРу 56 в разделе URL-адреса [и диапазоны IP-адресов Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).