---
title: Обзор службы "Настройка состояния службы автоматизации Azure"
description: Обзор DSC службы "Настройка состояния службы автоматизации Azure", условий использования и распространенных проблем
keywords: PowerShell DSC, настройка требуемого состояния, PowerShell DSC для Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b486c30827ee67b58cbdc0027c8221cceed02e51
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235941"
---
# <a name="azure-automation-state-configuration-overview"></a>Обзор службы "Настройка состояния службы автоматизации Azure"

"Настройка состояния службы автоматизации Azure" — это служба Azure, которая позволяет создавать и компилировать [конфигурации](/powershell/dsc/configurations)PowerShell Desired State Configuration (DSC), управлять ими, импортировать [ресурсы DSC](/powershell/dsc/resources), а также назначать конфигурации целевым узлам, и все это в облаке.

## <a name="why-use-azure-automation-state-configuration"></a>Преимущества службы "Настройка состояния службы автоматизации Azure"

Служба "Настройка состояния службы автоматизации Azure" предоставляет ряд преимуществ по сравнению с использованием DSC за пределами Azure.

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Служба "Настройка состояния службы автоматизации Azure" предоставляет опрашивающий сервер DSC, подобный [службе DSC компонента Windows](/powershell/dsc/pullserver), чтобы целевые узлы автоматически получали конфигурации, соответствовали требуемому состоянию и сообщали о соответствии. Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер. Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Служба "Настройка состояния службы автоматизации Azure" обеспечивает тот же слой управления для [Desired State Configuration PowerShell](/powershell/dsc/overview), который служба автоматизации Azure предлагает для скриптов PowerShell.

Вы можете управлять всеми конфигурациями, ресурсами и целевыми узлами DSC с помощью портала Azure или PowerShell.

![Снимок экрана со страницей службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Импорт данных отчетов в журналы Azure Monitor

Узлы, управление которыми осуществляется с помощью "Настройка состояния службы автоматизации Azure", отправляют подробные отчеты с данными о состоянии на встроенный опрашивающий сервер. В службе "Настройка состояния службы автоматизации Azure" можно настроить отправку этих данных в рабочую область Log Analytics. Чтобы узнать, как отправлять данные о состоянии конфигурации состояния в рабочую область Log Analytics, см. в разделе [вперед автоматизации настройки состояния Azure данные отчетов Azure Monitor журналы](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Технические условия

Рассмотрите следующие требования при использовании конфигурации состояния службы автоматизации Azure (DSC).

### <a name="operating-system-requirements"></a>Требования к операционной системе

Для узлов под управлением Windows поддерживаются следующие версии:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 с пакетом обновления 1
- Windows 10
- Windows 8.1
- Windows 7

Для узлов под управлением Linux поддерживаются следующих дистрибутивах и версиях:

Расширение DSC Linux поддерживает все дистрибутивы Linux [рекомендованные в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) за исключением:

Дистрибутив | Version
-|-
Debian  | Все версии.
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>Требования к DSC

Для всех узлов Windows, запущенных в Azure [WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) устанавливается во время подключения.  Для узлов под управлением Windows Server 2012 и Windows 7 [включается WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Для всех узлов Linux, работающих в Azure [PowerShell DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) устанавливается во время подключения.

### <a name="network-planning"></a>Настройка частных сетях

Если узлы находятся в частной сети, следующие порт и URL-адреса необходимы для состояния Configuration (DSC) для взаимодействия со службой автоматизации:

* Порт: только исходящий интернет-трафик через TCP-порт 443.
* Глобальный URL-адрес: *.azure-automation.net.
* Глобальный URL-адрес US Gov (Вирджиния): *.azure automation.us
* Служба агента: https://\<ИД рабочей области\>.agentsvc.azure-automation.net

#### <a name="proxy-support"></a>Поддержка прокси-сервера

Поддержка прокси-сервера для агента DSC доступна в Windows 1809 и более поздних версий.
Чтобы настроить этот параметр, задайте значение для **ProxyURL** и **ProxyCredential** в [метаконфигурации скрипт](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) использовать регистрацию узлов.
Прокси-сервер недоступен в DSC для предыдущих версий Windows.

Для узлов Linux агент DSC поддерживает прокси-сервера и будет использовать переменную http_proxy, чтобы определить URL-адрес.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Диапазоны сетей Azure состояние конфигурации и пространства имен

При определении исключений рекомендуется использовать адреса из списка. Если вам нужны IP-адреса, вы можете скачать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

При наличии учетной записи службы автоматизации, определенной для конкретного региона, можно ограничить обмен данными с таким региональным центром обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

| **Регион** | **Запись DNS** |
| --- | --- |
| Западно-центральная часть США | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Центрально-южная часть США |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Восток США 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Центральная Канада |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Западная Европа |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Северная Европа |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Азия |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Центральная Индия |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Восточная часть Японии |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Австралия |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Южная часть Великобритании | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Правительство штата Вирджиния | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Для списка IP-адресов региона вместо его имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним.

> [!NOTE]
> В XML-файле IP-адресов центра обработки данных Azure приводится список диапазонов IP-адресов, используемых в центрах обработки данных Microsoft Azure. Этот файл включает диапазоны вычисления, хранения и SQL.
>
>Обновленный файл публикуется еженедельно. Он отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю.
>
> Скачивайте новый XML-файл каждую неделю и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. Пользователям Azure ExpressRoute следует обратить внимание, что этот файл используется для того, чтобы обновлять протокол BGP в пространстве Azure в первую неделю каждого месяца.

## <a name="introduction-video"></a>Видео: общие сведения

Предпочитаете смотреть, а не читать? Посмотрите следующий видеоролик, выпущенный в мае 2015 г., когда впервые было объявлено о создании службы "Настройка состояния службы автоматизации Azure".

> [!NOTE]
> Несмотря на то, что основные концепции и жизненный цикл, о которых рассказывается в этом видеоролике, по-прежнему актуальны, с момента записи этого видеоролика служба "Настройка состояния службы автоматизации Azure" сделала большой шаг вперед. Теперь она стала общедоступной, оснащена более функциональным пользовательским интерфейсом на портале Azure и поддерживает множество дополнительных возможностей.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Дополнительные сведения о подключении узлов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
