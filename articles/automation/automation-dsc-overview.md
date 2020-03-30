---
title: Обзор службы "Настройка состояния службы автоматизации Azure"
description: Обзор DSC службы "Настройка состояния службы автоматизации Azure", условий использования и распространенных проблем
keywords: PowerShell DSC, настройка требуемого состояния, PowerShell DSC для Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 46cf0d6a12ffbc836db7bd79c0f2738a94e23085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283197"
---
# <a name="azure-automation-state-configuration-overview"></a>Обзор службы "Настройка состояния службы автоматизации Azure"

Система Azure Automation State Configuration — это служба Azure, которая позволяет писать, управлять и компилировать [конфигурации](/powershell/scripting/dsc/configurations/configurations)PowerShell Desired State Configuration (DSC). Служба также импортирует [DSC Ресурсы](/powershell/scripting/dsc/resources/resources)и назначает конфигурации для целевых узлов, все в облаке.

## <a name="why-use-azure-automation-state-configuration"></a>Преимущества службы "Настройка состояния службы автоматизации Azure"

Служба "Настройка состояния службы автоматизации Azure" предоставляет ряд преимуществ по сравнению с использованием DSC за пределами Azure.

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Конфигурация состояния автоматизации Azure обеспечивает сервер DSC pull, похожий на [Функцию Windows DSC-Service.](/powershell/scripting/dsc/pull-server/pullserver) Целевые узлы могут автоматически получать конфигурации, соответствовать желаемому состоянию и сообщать об их соответствии. Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер. Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Конфигурация состояния автоматизации Azure приносит тот же уровень управления [в конфигурацию состояния PowerShell Desired,](/powershell/scripting/dsc/overview/overview) что и для скриптов PowerShell. С портала Azure или от PowerShell вы можете управлять всеми конфигурациями, ресурсами и целевыми узлами DSC.

![Снимок экрана со страницей службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Импорт отчетных данных в журналы Azure Monitor

Узлы, управление которыми осуществляется с помощью "Настройка состояния службы автоматизации Azure", отправляют подробные отчеты с данными о состоянии на встроенный опрашивающий сервер. В службе "Настройка состояния службы автоматизации Azure" можно настроить отправку этих данных в рабочую область Log Analytics. Смотрите [данные о конфигурации состояния forward Azure Automation в журналах Azure Monitor.](automation-dsc-diagnostics.md)

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Предпосылки для использования конфигурации состояния автоматизации Azure

Пожалуйста, обратите сьязание следующих требований при использовании конфигурации состояния автоматизации Azure для DSC.

### <a name="operating-system-requirements"></a>Требования к операционной системе

Для узлов, работающих под управлением Windows, поддерживаются следующие версии:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2;
- Windows Server 2012
- Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Автономный продукт [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU не содержит реализации DSC. Таким образом, им не может управлять PowerShell DSC или Azure Automation State Configuration.

Для узлов под управлением Linux расширение DSC Linux поддерживает все дистрибутивы Linux, перечисленные в [поддерживаемых дистрибутивах Linux.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)

### <a name="dsc-requirements"></a>Требования DSC

Для всех узлов Windows, работающих в Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) устанавливается во время посадки. Для узлов под управлением Windows Server 2012 и Windows 7 [включен WinRM.](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)

Для всех узлов Linux, работающих в Azure, [PowerShell DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) устанавливается во время посадки.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Конфигурация частных сетей

Если ваши узлы расположены в частной сети, требуются следующие портивы и URL-адреса. Эти ресурсы обеспечивают подключение к сети управляемого узла и позволяют DSC общаться с Azure Automation.

* Порт: Только TCP 443 требуется для исходящего доступа в Интернет
* Глобальный URL-адрес:**.azure-automation.net**
* Глобальный URL-адрес губернатора США Вирджинии:**.azure-automation.us**
* Служба агента: **https://\<\>workspaceId .agentsvc.azure-automation.net**

Если вы используете ресурсы DSC, которые взаимодействуют между узлами, такие как [ресурсы WaitFor,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)необходимо также разрешить трафик между узлами. Ознакомьтесь с документацией для каждого ресурса DSC, чтобы понять эти сетевые требования.

#### <a name="proxy-support"></a>Поддержка прокси

Прокси-поддержка агента DSC доступна в версии Windows 1809 и позже. Эта опция включена путем `ProxyURL` `ProxyCredential` установки значений для и в [сценарии метаконфигурации,](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) используемого для регистрации узлов.

>[!NOTE]
>Конфигурация состояния автоматизации Azure не обеспечивает поддержку прокси-серверов DSC для предыдущих версий Windows.

Для узлов Linux агент DSC поддерживает прокси `http_proxy` и использует переменную для определения URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Диапазоны и пространство имен Azure Automation Автоматизация состояния

При определении исключений рекомендуется использовать указанные ниже адреса. Для IP-адресов можно загрузить [IP Ranges Ip Ranges Центра обработки данных Microsoft Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

При наличии учетной записи службы автоматизации, определенной для конкретного региона, можно ограничить обмен данными с таким региональным центром обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

| **Регионе** | **Запись DNS** |
| --- | --- |
| центрально-западная часть США | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Центрально-южная часть США |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Восточная часть США    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| восточная часть США 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Центральная Канада |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Западная Европа |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Северная Европа |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Азия |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Центральная Индия |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Восточная Япония |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Австралия |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| южная часть Соединенного Королевства | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov (Вирджиния) | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Для списка IP-адресов региона вместо его имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним.

> [!NOTE]
> В XML-файле IP-адресов центра обработки данных Azure приводится список диапазонов IP-адресов, используемых в центрах обработки данных Microsoft Azure. Этот файл включает диапазоны вычисления, хранения и SQL.
>
>Обновленный файл публикуется еженедельно. Он отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю. Скачивайте новый XML-файл каждую неделю Затем обновите свой сайт, чтобы правильно идентифицировать службы, работающие в Azure. 

Пользователям Azure ExpressRoute следует обратить внимание, что этот файл используется для того, чтобы обновлять протокол BGP в пространстве Azure в первую неделю каждого месяца.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы начать использовать DSC в конфигурации состояния автоматизации Azure, см. [Начало работы с конфигурацией состояния azure Automation State Configuration.](automation-dsc-getting-started.md)
- Чтобы узнать, как на [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md)бортовых узлах, см.
- Чтобы узнать о компиляции конфигураций DSC, чтобы можно было назначить их целевым узлам, см. [Конфигурации компиляции в конфигурации состояния azure Automation.](automation-dsc-compile.md)
- Для справки PowerShell cmdlet [см.](/powershell/module/azurerm.automation/#automation)
- Для получения информации о ценах на цены [см.](https://azure.microsoft.com/pricing/details/automation/)
- Чтобы увидеть пример использования конфигурации состояния автоматизации Azure в непрерывном конвейере развертывания, [см. Непрерывное развертывание с помощью конфигурации состояния azure Automation и Chocolatey.](automation-dsc-cd-chocolatey.md)
