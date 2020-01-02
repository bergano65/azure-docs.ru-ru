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
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850896"
---
# <a name="azure-automation-state-configuration-overview"></a>Обзор службы "Настройка состояния службы автоматизации Azure"

"Настройка состояния службы автоматизации Azure" — это служба Azure, которая позволяет создавать и компилировать [конфигурации](/powershell/scripting/dsc/configurations/configurations)PowerShell Desired State Configuration (DSC), управлять ими, импортировать [ресурсы DSC](/powershell/scripting/dsc/resources/resources), а также назначать конфигурации целевым узлам, и все это в облаке.

## <a name="why-use-azure-automation-state-configuration"></a>Преимущества службы "Настройка состояния службы автоматизации Azure"

Служба "Настройка состояния службы автоматизации Azure" предоставляет ряд преимуществ по сравнению с использованием DSC за пределами Azure.

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Служба "Настройка состояния службы автоматизации Azure" предоставляет опрашивающий сервер DSC, подобный [службе DSC компонента Windows](/powershell/scripting/dsc/pull-server/pullserver), чтобы целевые узлы автоматически получали конфигурации, соответствовали требуемому состоянию и сообщали о соответствии. Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер. Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Служба "Настройка состояния службы автоматизации Azure" обеспечивает тот же слой управления для [Desired State Configuration PowerShell](/powershell/scripting/dsc/overview/overview), который служба автоматизации Azure предлагает для скриптов PowerShell.

Вы можете управлять всеми конфигурациями, ресурсами и целевыми узлами DSC с помощью портала Azure или PowerShell.

![Снимок экрана со страницей службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Импорт данных отчетов в журналы Azure Monitor

Узлы, управление которыми осуществляется с помощью "Настройка состояния службы автоматизации Azure", отправляют подробные отчеты с данными о состоянии на встроенный опрашивающий сервер. В службе "Настройка состояния службы автоматизации Azure" можно настроить отправку этих данных в рабочую область Log Analytics. Сведения о том, как отправлять данные о состоянии конфигурации состояния в рабочую область Log Analytics, см. в разделе [пересылка данных отчетов о настройке состояния службы автоматизации Azure в журналы Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Технические условия

При использовании конфигурации состояния службы автоматизации Azure (DSC) учитывайте следующие требования.

### <a name="operating-system-requirements"></a>Требования к операционной системе

Для узлов под управлением Windows поддерживаются следующие версии:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 с пакетом обновления 1;
- Windows 10
- Windows 8.1
- Windows 7

Номер SKU автономного продукта [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) не содержит реализацию Desired State Configuration, поэтому управлять им с помощью DSC PowerShell или настройки состояния службы автоматизации Azure невозможно.

Для узлов под управлением Linux поддерживаются следующие дистрибутивов и версии:

Расширение DSC для Linux поддерживает все дистрибутивы Linux, перечисленные в разделе [Поддерживаемые дистрибутивы Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Требования к DSC

Для всех узлов Windows, работающих в Azure, во время адаптации будет установлен [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) .  Для узлов под Windows Server 2012 и Windows 7 [Служба WinRM будет включена](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Для всех узлов Linux, работающих в Azure, [POWERSHELL DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) будет установлен во время адаптации.

### <a name="network-planning"></a>Настройка частных сетей

Если узлы находятся в частной сети, для взаимодействия с автоматизацией требуется следующий порт и URL-адреса для настройки состояния (DSC):

* порт: только исходящий трафик Интернета через TCP-порт 443.
* Глобальный URL-адрес: *.azure-automation.net.
* Глобальный URL-адрес US Gov (Вирджиния): *.azure automation.us
* Служба агента: https://\<ИД рабочей области\>.agentsvc.azure-automation.net

Это обеспечивает сетевое подключение для управляемого узла для взаимодействия со службой автоматизации Azure.
При использовании ресурсов DSC, взаимодействующих между узлами, например с [ресурсами WAITFOR *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), необходимо также разрешить трафик между узлами.
Сведения о требованиях к сети см. в документации по каждому ресурсу DSC.

#### <a name="proxy-support"></a>Поддержка прокси-сервера

Поддержка прокси-сервера для агента DSC доступна в Windows версии 1809 и более поздних версиях.
Чтобы настроить этот параметр, задайте значение для **прокси** и **ProxyCredential** в [скрипте метаконфигурации](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) , используемом для регистрации узлов.
Прокси-сервер недоступен в DSC для предыдущих версий Windows.

Для узлов Linux агент DSC поддерживает прокси-сервер и будет использовать переменную http_proxy для определения URL-адреса.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Диапазоны и пространство имен сети конфигурации состояния Azure

При определении исключений рекомендуется использовать адреса из списка. Если вам нужны IP-адреса, вы можете скачать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

При наличии учетной записи службы автоматизации, определенной для конкретного региона, можно ограничить обмен данными с таким региональным центром обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

| **Регион** | **Запись DNS** |
| --- | --- |
| Центрально-западная часть США | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Центрально-южная часть США |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Восточная часть США   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Восточная часть США 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Центральная Канада |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Западная Европа |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Северная Европа |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Азия |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Центральная Индия |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Восточная Япония |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Австралия |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Южная часть Соединенного Королевства | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov (Вирджиния) | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Для списка IP-адресов региона вместо его имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним.

> [!NOTE]
> В XML-файле IP-адресов центра обработки данных Azure приводится список диапазонов IP-адресов, используемых в центрах обработки данных Microsoft Azure. Этот файл включает диапазоны вычисления, хранения и SQL.
>
>Обновленный файл публикуется еженедельно. Он отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю.
>
> Скачивайте новый XML-файл каждую неделю и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. Пользователям Azure ExpressRoute следует обратить внимание, что этот файл используется для того, чтобы обновлять протокол BGP в пространстве Azure в первую неделю каждого месяца.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Дополнительные сведения о подключении узлов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
