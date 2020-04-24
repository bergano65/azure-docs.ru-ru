---
description: Сведения о журнале версий расширения Desired State Configuration (DSC) в Azure.
ms.date: 06/21/2018
keywords: dsc, powershell, azure, расширение
title: Журнал версий расширения Azure DSC
author: mgoedtel
ms.author: magoedte
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: 207b8f7467753a8693bbabe6dd3f7a890307cf21
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120708"
---
# <a name="azure-desired-state-configuration-extension-version-history"></a>Журнал версий расширения Desired State Configuration Azure

Расширение виртуальной машины Desired State Configuration (DSC) Azure обновляется по мере необходимости для поддержки улучшений и новых возможностей в Azure, Windows Server и среде Windows Management Framework (WMF), в состав которой входит Windows PowerShell.

В этой статье содержатся сведения о каждой версии расширения виртуальной машины Azure DSC, поддерживаемых средах, а также приводятся комментарии и примечания о новых функциях или изменениях.

## <a name="latest-version"></a>Последняя версия

### <a name="version-276"></a>Версия 2.76

- **Дата выпуска:**
  - 9 мая 2018 г. (Azure) | 21 июня 2018 г. (Azure для Китая, Azure для государственных организаций)
- **Поддержка ОС:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 с пакетом обновления 1 (SP1)
  - Клиент Windows 7, 8.1, 10
  - Сервер Nano Server
- **Поддержка WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Обновление WMF 4.0
  - WMF 4.0
- **PXE**
  - Azure
  - Azure для Китая
  - Azure для государственных организаций
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Улучшения в метаданных расширения для подсостояния и другие незначительные исправления ошибок.

## <a name="supported-versions"></a>Поддерживаемые версии

> [!WARNING]
> В версиях 2,4 – 2,13 для WMF 5,0 используется общедоступная Предварительная версия, срок действия сертификатов подписи которых истек в августе
> 2016. Дополнительные сведения об этой проблеме см. в [этой записи блога](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Версия 2.75

- **Дата выпуска**: 5 марта 2018 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Windows Client 7, 8.1, 10, Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - После недавнего перехода GitHub на TLS 1.2 виртуальную машину невозможно присоединить к DSC в службе автоматизации Azure с помощью шаблонов DIY Resource Manager, доступных в Azure Marketplace, или использовать расширение DSC для получения конфигураций, размещенных на сайте GitHub. При развертывании расширения появится ошибка следующего вида:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - TLS 1.2 принудительно используется в новой версии расширения. Если при развертывании расширения в шаблоне Resource Manager использовалось значение AutoUpgradeMinorVersion = true, расширение автоматически обновится до версии 2.75. Чтобы выполнить обновление вручную, укажите `TypeHandlerVersion = 2.75` в шаблоне Resource Manager.

### <a name="version-270---272"></a>Версии с 2.70 по 2.72

- **Дата выпуска**: 13 ноября 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Windows Client 7, 8.1, 10, Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Исправления ошибок и улучшения, упрощающие использование с DSC в службе автоматизации Azure с помощью пользовательского интерфейса портала и шаблона Resource Manager. Дополнительные сведения см. в разделе о [скрипте конфигурации по умолчанию](/azure/virtual-machines/extensions/dsc-overview) в документации по расширению DSC.

### <a name="version-226"></a>Версия 2.26

- **Дата выпуска**: 9 июня 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Windows Client 7, 8.1, 10, Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Усовершенствования телеметрии.

### <a name="version-225"></a>Версия 2.25

- **Дата выпуска:** 2 июня 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Windows Client 7, 8.1, 10, Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Было добавлено несколько исправлений ошибок и ряд других незначительных улучшений.

### <a name="version-224"></a>Версия 2.24

- **Дата выпуска**: 13 апреля 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Предоставляет UUID виртуальной машины и идентификатор агента DSC как метаданные расширения. Были добавлены другие незначительные улучшения.

### <a name="version-223"></a>Версия 2.23

- **Дата выпуска**: 15 марта 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Было добавлено множество исправлений ошибок и других улучшений.

### <a name="version-222"></a>Версия 2.22

- **Дата выпуска**: 8 февраля 2017 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Nano Server
- **Поддержка WMF**: WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Расширение DSC теперь имеет поддержку для WMF 5.1.
  - Были добавлены другие незначительные улучшения.

### <a name="version-221"></a>Версия 2.21

- **Дата выпуска**: 2 декабря 2016 г.
- **Поддержка ОС**: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1), Nano Server
- **Поддержка WMF**: предварительная версия WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка). В случае Nano Server роль DSC устанавливается на виртуальной машине.
- **Новые возможности:**
  - Теперь расширение DSC доступно в ОС Nano Server. Эта версия в основном содержит изменения кода для запуска расширения на сервере Nano Server.
  - Были добавлены другие незначительные улучшения.

### <a name="version-220"></a>Версия 2.20

- **Дата выпуска**: 2 августа 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: предварительная версия WMF 5.1, WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Поддержка предварительной версии WMF 5.1. При первой публикации эта версия являлась необязательным обновлением, и для установки предварительной версии WMF 5.1 приходилось указывать Wmfversion = "5.1PP" в шаблонах Resource Manager.
    Выбор Wmfversion = "latest" по-прежнему приводит к установке [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Дополнительные сведения о предварительной версии WMF 5.1 см. в [этом блоге](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Были добавлены другие незначительные исправления и улучшения.

### <a name="version--219"></a>Версия 2.19

- **Дата выпуска**: 3 июня 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure, Azure для Китая, Azure для государственных организаций
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Теперь расширение DSC подключено в Azure для Китая. Эта версия в основном содержит исправления для запуска расширения в Azure для Китая.

### <a name="version-218"></a>Версия 2.18

- **Дата выпуска**: 3 июня 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Неблокируемость телеметрии при возникновении ошибки во время скачивания исправления телеметрии (известная проблема Azure DNS) или во время установки.
  - Исправлена временная проблема, когда расширение прекращало обработку конфигурации после перезагрузки.
    После этого расширение DSC оставалось в переходном состоянии.
  - Были добавлены другие незначительные исправления и улучшения.

### <a name="version-217"></a>Версия 2.17

- **Дата выпуска**: 26 апреля 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, обновление WMF 4.0, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Поддержка обновления WMF 4.0. Дополнительные сведения об обновлениях WMF 4.0 см. в [этом блоге](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Реализация логики повторов при ошибках, возникающих во время установки расширения DSC или во время применения конфигурации DSC после установки расширения. В рамках этого изменения расширение будет повторять установку в случае сбоя предыдущей установки или повторно активировать ранее неудачную конфигурацию DSC не более трех раз до перехода в состояние завершения (успех или ошибка) либо до поступления нового запроса. Если работа расширения завершается ошибкой из-за недопустимых параметров или введенных данных, повторная попытка не предпринимается. В этом случае расширение необходимо вызывать еще раз с помощью нового запроса и правильных параметров пользователя. Примечание. Выполнение повторных попыток расширения DSC зависит от агента виртуальных машин. Агент виртуальных машин Azure вызывает расширение с использованием последнего неудачного запроса, пока оно не перейдет в состояние успеха или ошибки.

### <a name="version-216"></a>Версия 2.16

- **Дата выпуска**: 21 апреля 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Улучшения в обработке ошибок и другие незначительные исправления ошибок.
  - Новое свойство в параметрах расширения DSC. Добавлен параметр ForcePullAndApply в AdvancedOptions, позволяющий расширению DSC применять конфигурации DSC в режиме обновления Pull (в отличие от режима Push, используемого по умолчанию). Дополнительные сведения о параметрах расширения DSC см. в [этом блоге](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Версия 2.15

- **Дата выпуска**: 14 марта 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - В версию 2.14 расширения были внесены изменения в установку WMF RTM. При обновлении с версии расширения 2.13.2.0 до 2.14.0.0 можно заметить, что выполнение некоторых командлетов DSC или конфигурации завершается ошибкой — "Экземпляр с заданными значениями свойств не найден". Дополнительные сведения см. в [заметках о выпуске DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Способы решения этих проблем были добавлены в версию 2.15.
  - К сожалению, если вы не установили версию 2.14 и у вас возникла одна из двух приведенных выше проблем, вам потребуется выполнить указанные действия вручную. В сеансе PowerShell с повышенными привилегиями:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Версия 2.14

- **Дата выпуска**: 25 февраля 2016 г.
- **Поддержка ОС**: Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- **Поддержка WMF**: WMF 5.0 RTM, WMF 4.0
- **Среда**: Azure
- **Примечания**. В этой версии расширение DSC используется в составе Windows Server 2016 Technical Preview. Для других операционных систем Windows устанавливается среда [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (для установки WMF требуется перезагрузка).
- **Новые возможности:**
  - Использование WMF RTM.
  - Включен сбор данных для повышения качества расширения DSC. Дополнительные сведения см. в [этом блоге](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Обновленный формат параметров для расширения в шаблоне Resource Manager. Дополнительные сведения см. в [этом блоге](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Исправления ошибок и другие усовершенствования.

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о PowerShell DSC см. в [центре документации по PowerShell](/powershell/scripting/dsc/overview/overview).
- Изучите [шаблон Resource Manager для расширения DSC](/azure/virtual-machines/extensions/dsc-template).
- Дополнительные функциональные возможности, которыми можно управлять с помощью PowerShell DSC, и ресурсы по DSC см. в [коллекции PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Сведения о передаче конфиденциальных параметров в конфигурации см. в статье о [безопасном управлении учетными данными с помощью обработчика расширения DSC](/azure/virtual-machines/extensions/dsc-credentials).
