---
title: Сравнить базовые показатели с мониторинг целостности файлов в центре безопасности Azure | Документация Майкрософт
description: Узнайте, как сравнивать базовые показатели с мониторинг целостности файлов в центре безопасности Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358443"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Сравнить базовые планы, с помощью мониторинга целостности файлов (FIM)

Мониторинг целостности файлов (FIM) информирует о том, при изменениях к важным областям в списке ресурсов, чтобы вы могли изучить и устранить несанкционированные действия. FIM отслеживает файлы Windows, Windows реестров и файлов Linux.

В этом разделе объясняется, как включить FIM для файлов и реестров. Дополнительные сведения о FIM см. в разделе [мониторинг целостности файлов в центре безопасности Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Зачем использовать FIM?

Операционной системы, приложений и связанные с ним конфигурации управления состоянием поведение и безопасности ваших ресурсов. Таким образом злоумышленники целевые файлы, которые управляют ресурсами, чтобы стала ресурса операционной системы и/или выполнения действий этого не.

На самом деле различных стандартов соответствия нормативным требованиям, например PCI-DSS и ISO 17799 требуют реализации элементов управления в FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Включить встроенные рекурсивной проверки реестра

Куст реестра FIM значения по умолчанию предоставляют удобный способ наблюдения за изменениями рекурсивного в общих областей безопасности.  Например злоумышленник может настроить скрипт для выполнения в контексте LOCAL_SYSTEM путем настройки выполнения при запуске или завершении работы.  Чтобы отслеживать изменения этого типа, включите встроенную проверку.  

![Реестр](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Рекурсивной проверки применяются только к безопасности кусты, а не к пути реестра для настраиваемой.  

## <a name="adding-a-custom-registry-check"></a>Добавление проверки пользовательского реестра

Базовые показатели FIM начните с определения характеристик удачного состояния для операционной системы и вспомогательные приложения.  В этом примере мы сосредоточимся на настройки политики паролей для Windows Server 2008 и более поздних версий.


|Имя политики                 | Параметр реестра|
|---------------------------------------|-------------|
|Контроллер домена: Запретить изменение пароля учетной записи компьютера| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|член домена Цифровая подпись или шифрование данных безопасного канала (всегда)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|член домена Шифрование данных безопасного канала (если возможно)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|член домена Цифровая подпись данных безопасного канала (если возможно)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|член домена Отключить изменение пароля учетной записи компьютера|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|член домена Машины максимальный срок действия пароля учетной записи|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|член домена Требует стойкого ключа сеанса (Windows 2000 или более поздней версии)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Сетевая безопасность: Ограничения NTLM:  Проверка подлинности NTLM в этом домене|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Сетевая безопасность: Ограничения NTLM: Добавить исключения для серверов в этом домене|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Сетевая безопасность: Ограничения NTLM: Аудит проверки подлинности NTLM в этом домене|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Дополнительные сведения о параметрах реестра, поддерживаемых различными версиями операционной системы, см. [параметры групповой политики ссылаются на таблицы](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Чтобы настроить FIM для наблюдения за базовые показатели реестра:*

1. В **Добавление реестра Windows для отслеживания изменений** окно в **раздел реестра Windows** текста введите раздел реестра.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Включить FIM в реестре](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Отслеживание изменений в файлы Windows

1. В **Добавление файла Windows для отслеживания изменений** окно в **путь ввод** текстовое поле, укажите папку, в котором содержатся файлы, которые вы хотите отслеживать. В примере на рисунке ниже **веб-приложение Contoso** находится на D:\ диск в **ContosWebApp** структуру папок.  
1. Создайте пользовательскую запись файла Windows, предоставляя имя класса параметр, включение рекурсии и указав папку верхнего уровня с суффиксом подстановочный знак (*).

    ![Включить FIM с файлом](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Получение информации об изменениях

Данные будут находиться в Azure Log Analytics мониторинг целостности файлов / set ConfigurationChange таблицы.  

 1. Задайте диапазон времени, чтобы получить сводку по изменениям, ресурсов.
В следующем примере извлекаются все изменения за последние Четырнадцать дней в категориях реестра и файлов:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Чтобы просмотреть сведения об изменении реестра:

    1. Удалить **файлы** из **где** предложения, 
    1. Удалите строку формирования сводных данных и замените его предложение сортировки:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Отчеты можно экспортировать в CSV-ФАЙЛ для архивации и/или направляться к отчета Power BI.  

![Данных FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)