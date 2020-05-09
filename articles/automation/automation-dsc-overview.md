---
title: Обзор службы "Настройка состояния службы автоматизации Azure"
description: Обзор конфигурации состояния службы автоматизации Azure, ее условий и известных проблем
keywords: PowerShell DSC, настройка требуемого состояния, PowerShell DSC для Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dbe617e6614eb69f0a7f6e31c89c1f645804fe1b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993853"
---
# <a name="azure-automation-state-configuration-overview"></a>Обзор службы "Настройка состояния службы автоматизации Azure"

Настройка состояния службы автоматизации Azure — это служба управления конфигурацией Azure, которая позволяет создавать, управлять и компилировать конфигурации Desired State Configuration (DSC [) для](/powershell/scripting/dsc/configurations/configurations) узлов в любом облачном или локальном центре обработки данных. Служба также импортирует [ресурсы DSC](/powershell/scripting/dsc/resources/resources)и назначает конфигурации целевым узлам в облаке. Вы можете получить доступ к конфигурации состояния службы автоматизации Azure в портал Azure, выбрав **Configuration State (DSC)** в разделе **Управление конфигурацией**. 

Вы можете использовать конфигурацию состояния службы автоматизации Azure для управления различными компьютерами:

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Физические или виртуальные машины Windows в локальной среде или в облаке, отличном от Azure (включая экземпляры AWS EC2).
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Если вы не готовы управлять конфигурацией компьютера из облака, можно использовать конфигурацию состояния службы автоматизации Azure в качестве конечной точки только для отчетов. Эта функция позволяет задавать (отправлять) конфигурации через DSC и просматривать сведения о отчетах в службе автоматизации Azure.

> [!NOTE]
> Управление виртуальными машинами Azure с конфигурацией состояния службы автоматизации Azure включается без дополнительной платы, если установленная версия расширения Desired State Configuration для виртуальной машины Azure превышает 2,70. Дополнительные сведения см. на [**странице с ценами на автоматизацию**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Преимущества службы "Настройка состояния службы автоматизации Azure"

Настройка состояния службы автоматизации Azure предоставляет несколько преимуществ по сравнению с использованием DSC за пределами Azure. Эта служба обеспечивает возможность быстрого и простого масштабирования на тысячах компьютеров из центрального и безопасного расположения. Вы можете легко включить компьютеры, назначить им декларативные конфигурации и просмотреть отчеты, показывающие соответствие каждого компьютера указанному вами состоянию.

Служба настройки состояния службы автоматизации Azure предназначена для DSC, какие модули Runbook в службе автоматизации Azure входят в скрипты PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. 

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Конфигурация состояния службы автоматизации Azure предоставляет опрашивающий сервер DSC, аналогичный [компоненту Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Целевые узлы могут автоматически принимать конфигурации, соответствовать требуемому состоянию и сообщать о соответствии. Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер. Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Конфигурация состояния службы автоматизации Azure использует тот же уровень управления для [настройки требуемого состояния PowerShell](/powershell/scripting/dsc/overview/overview) , как это предлагается для сценариев PowerShell. С помощью портал Azure или из PowerShell можно управлять всеми конфигурациями, ресурсами и целевыми узлами DSC.

![Снимок экрана со страницей службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Импорт данных отчетов в журналы Azure Monitor

Узлы, управление которыми осуществляется с помощью "Настройка состояния службы автоматизации Azure", отправляют подробные отчеты с данными о состоянии на встроенный опрашивающий сервер. В службе "Настройка состояния службы автоматизации Azure" можно настроить отправку этих данных в рабочую область Log Analytics. См. раздел [пересылка данных отчетов о настройке состояния службы автоматизации Azure в журналы Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Необходимые условия для использования конфигурации состояния службы автоматизации Azure

При использовании конфигурации состояния службы автоматизации Azure учитывайте требования, описанные в этом разделе.

### <a name="operating-system-requirements"></a>Требования к операционной системе

Для узлов под управлением Windows поддерживаются следующие версии:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- быть под управлением ОС Windows 10;
- Windows 8.1
- Windows 7

>[!NOTE]
>Номер SKU автономного продукта [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) не содержит реализацию DSC. Поэтому управление им с помощью PowerShell DSC или конфигурации состояния службы автоматизации Azure невозможно.

Для узлов под управлением Linux расширение DSC для Linux поддерживает все дистрибутивы Linux, перечисленные в разделе [Поддерживаемые дистрибутивы Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Требования к DSC

Для всех узлов Windows, работающих в Azure, при включении компьютеров устанавливается [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) . Для узлов под Windows Server 2012 и Windows 7 [Служба WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) включена.

Для всех узлов Linux, работающих в Azure, при включении компьютеров устанавливается [POWERSHELL DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Настройка частных сетей

Если узлы находятся в частной сети, требуются следующие порты и URL-адреса. Эти ресурсы обеспечивают сетевое подключение для управляемого узла и позволяют DSC обмениваться данными со службой автоматизации Azure.

* Порт: для исходящего доступа в Интернет требуется только TCP 443
* Глобальный URL-адрес: ***. Azure-Automation.NET**
* Глобальный URL-адрес US Gov (Вирджиния): ***. Azure-Automation.US**
* Служба агента: **https://\<workspaceId\>. agentsvc.Azure-Automation.NET**

При использовании ресурсов DSC, взаимодействующих между узлами, например с [ресурсами WAITFOR *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), необходимо также разрешить трафик между узлами. Сведения о требованиях к сети см. в документации по каждому ресурсу DSC.

#### <a name="proxy-support"></a>Поддержка прокси-сервера

Поддержка прокси-сервера для агента DSC доступна в Windows версии 1809 и более поздних версиях. Этот параметр включается путем задания значений для `ProxyURL` и `ProxyCredential` в [скрипте метаконфигурации](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) , используемом для регистрации узлов.

>[!NOTE]
>В конфигурации состояния службы автоматизации Azure не предусмотрена поддержка прокси-сервера DSC для предыдущих версий Windows.

Для узлов Linux агент DSC поддерживает прокси-сервер и использует `http_proxy` переменную для определения URL-адреса.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Диапазоны и пространство имен сети конфигурации состояния службы автоматизации Azure

При определении исключений рекомендуется использовать указанные ниже адреса. Чтобы получить IP-адреса, можно скачать [диапазоны IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

При наличии учетной записи службы автоматизации, определенной для конкретного региона, можно ограничить обмен данными с таким региональным центром обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

| **Регион** | **Запись DNS** |
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
>Обновленный файл публикуется еженедельно. Он отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю. Скачивайте новый XML-файл каждую неделю Затем обновите сайт, чтобы правильно выбрать службы, работающие в Azure. 

Пользователям Azure ExpressRoute следует обратить внимание, что этот файл используется для того, чтобы обновлять протокол BGP в пространстве Azure в первую неделю каждого месяца.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе с DSC в конфигурации состояния службы автоматизации Azure, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Сведения о том, как включить узлы, см. в разделе [Включение управления для компьютеров с помощью конфигурации состояния службы автоматизации Azure](automation-dsc-onboarding.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [непрерывное развертывание с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)развертывания.
