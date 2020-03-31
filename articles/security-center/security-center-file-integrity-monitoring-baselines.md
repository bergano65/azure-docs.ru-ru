---
title: Мониторинг целостности файлов в центре безопасности Azure
description: Узнайте, как сравнить базовые данные с мониторингом целостности файлов в Центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664405"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Сравнение базовых показателей с помощью мониторинга целостности файлов

Мониторинг целостности файлов (FIM) информирует вас о том, когда происходят изменения в чувствительных областях в ваших ресурсах, так что вы можете исследовать и решать несанкционированные действия. FIM отслеживает файлы Windows, реестры Windows и Linux.

В этой теме объясняется, как включить FIM в файлы и реестры. Для получения дополнительной информации о FIM смотрите [мониторинг целостности файлов в Центре безопасности Azure.](security-center-file-integrity-monitoring.md)

## <a name="why-use-fim"></a>Зачем использовать FIM?

Операционная система, приложения и связанные с ними конфигурации контролируют состояние поведения и безопасности ресурсов. Таким образом, злоумышленники нацеливают файлы, контролирующие ваши ресурсы, чтобы обогнать операционную систему ресурса и/или выполнить действия без обнаружения.

На самом деле, многие нормативные стандарты соответствия, такие как PCI-DSS & ISO 17799 требуют внедрения контроля FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Включить встроенные рекурсивные проверки реестра

По умолчанию реестра FIM обеспечивает удобный способ мониторинга рекурсивных изменений в общих областях безопасности.  Например, противник может настроить сценарий для выполнения в LOCAL_SYSTEM контексте путем настройки выполнения при запуске или завершении.  Чтобы отслеживать изменения этого типа, включите встроенный чек.  

![Реестр](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Рекурсивные проверки применяются только к рекомендуемым ульям безопасности, а не к пользовательским путям реестра.  

## <a name="adding-a-custom-registry-check"></a>Добавление пользовательской проверки реестра

Базовые линии FIM начинаются с определения характеристик известного хорошего состояния для операционной системы и поддерживающего приложения.  В этом примере мы сосредоточимся на конфигурациях политики паролей для Windows Server 2008 и выше.


|Имя политики                 | Параметр реестра|
|---------------------------------------|-------------|
|Контроллер домена: запретить изменение пароля учетных записей компьютера.| MACHINE-System-CurrentControlSet-Услуги (Netlogon)Параметры /ОтказPasswordChange|
|Член домена: всегда требуется цифровая подпись или шифрование потока данных безопасного канала.|MACHINE-System-CurrentControlSet-Услуги (Netlogon-Parameters)RequireSignOrSeal|
|Член домена: шифрование данных безопасного канала, когда это возможно|MACHINE-System-CurrentControlSet/Услуги «Нетлогон»Параметры »SealSecureChannel|
|Член домена: цифровая подпись данных безопасного канала, когда это возможно|MACHINE-System-CurrentControlSet-Услуги «Нетлогон»Параметры »SignSecureChannel|
|Член домена: отключить изменение пароля учетных записей компьютера.|MACHINE-System-CurrentControlSet/Услуги «Нетлогон»Параметры »DisablePasswordChange|
|Член домена: максимальный срок действия пароля учетных записей компьютера|MACHINE-System-CurrentControlSet-Услуги «Нетлогон»Параметры »МаксимальныйPasswordAge|
|Член домена: требовать стойкий сеансовый ключ (Windows 2000 или выше)|MACHINE-System-CurrentControlSet-Услуги (Netlogon-Parameters)RequireStrongKey|
|Сетевая безопасность: Ограничить NTLM: ntLM аутентификации в этом домене|MACHINE-System-CurrentControlSet/Услуги «Нетлогон»Параметры/ОграничениеNTЛМинДом|
|Сетевая безопасность: ограничения NTLM: добавить исключения для серверов в этом домене.|MACHINE-System-CurrentControlSet/Услуги «Нетлогон»Параметры »DCAllowedNTLMServers|
|Сетевая безопасность: ограничения NTLM — аудит проверки подлинности NTLM на этом домене|MACHINE-System-CurrentControlSet/Услуги «Нетлогон»Параметры/AuditNTLMInDomain|

> [!NOTE]
> Чтобы узнать больше о настройках реестра, поддерживаемых различными версиями операционных систем, обратитесь к [таблице ссылки на настройки групповых параметров.](https://www.microsoft.com/download/confirmation.aspx?id=25250)

*Настройка FIM для мониторинга базовых линий реестра:*

1. В **добавленном реестре Windows для отслеживания изменений** в текстовом окне **ключа реестра Windows** введите ключ реестра.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Включить FIM в реестр](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Отслеживание изменений в файлах Windows

1. В окне **добавления Windows для отслеживания изменений** в текстовом поле **Пути Enter** введите папку, которая содержит файлы, которые вы хотите отследить. В примере в следующей цифре, **Contoso Web App** находится в D: диск в структуре папки **ContosWebApp.**  
1. Создайте пользовательскую запись файла Windows, предоставив имя класса настройки, что позволяет рекурсию и упомянув верхнюю папку с суффиксом подстановочного знака.

    ![Включить FIM в файл](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Извлечение данных изменений

Данные мониторинга целостности файлов находятся в таблице azure Log Analytics / ConfigurationChange.  

 1. Установите временной диапазон для получения резюме изменений по ресурсам.
В следующем примере мы извлекаем все изменения за последние четырнадцать дней в категориях реестра и файлов:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Для просмотра сведений об изменениях реестра:

    1. Удалить **файлы** из положения **о том, где,** 
    1. Удалите строку суммирования и замените ее оговоркой о заказе:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Отчеты могут быть экспортированы в CSV для архивирования и/или направляется в отчет Power BI.  

![Данные FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)