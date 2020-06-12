---
title: Обзор службы State Configuration службы автоматизации Azure
description: Эта статья содержит общие сведения о службе State Configuration службы автоматизации Azure.
keywords: PowerShell DSC, настройка требуемого состояния, PowerShell DSC для Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9880915061c0639aebe30bdb33258d7c79e155d7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836895"
---
# <a name="azure-automation-state-configuration-overview"></a>Обзор службы State Configuration службы автоматизации Azure

State Configuration службы автоматизации Azure — это служба управления конфигурацией Azure, которая позволяет создавать и компилировать [конфигурации](/powershell/scripting/dsc/configurations/configurations) PowerShell Desired State Configuration (DSC) и управлять ими для узлов в любом облаке или локальном центре обработки данных. Служба также импортирует [ресурсы DSC](/powershell/scripting/dsc/resources/resources) и назначает конфигурации целевым узлам, и все это в облаке. Вы можете получить доступ к службе State Configuration службы автоматизации Azure на портале Azure, выбрав **State Configuration (DSC)** в разделе **Управление конфигурацией**. 

Службу State Configuration службы автоматизации Azure можно использовать для управления разными компьютерами:

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Физические или виртуальные машины под управлением Windows, расположенные локально или в облачной службе, отличной от Azure (в том числе экземплярах AWS EC2)
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Если вы не готовы управлять конфигурацией компьютера из облака, служба State Configuration службы автоматизации Azure может также использоваться как конечная точка только для отчетности. Эта функция позволяет задавать (отправлять) конфигурации через DSC и просматривать сведения об отчетах в службе автоматизации Azure.

> [!NOTE]
> Управление виртуальными машинами Azure с помощью службы State Configuration службы автоматизации Azure включается без дополнительной платы, если установлена версия расширения Desired State Configuration для виртуальной машины Azure позднее 2.70. См. дополнительные сведения на [**странице с ценами на службу автоматизации**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Преимущества службы "Настройка состояния службы автоматизации Azure"

Служба State Configuration службы автоматизации Azure предоставляет ряд преимуществ по сравнению с использованием DSC за пределами Azure. Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.

Служба State Configuration службы автоматизации Azure связана с DSC так же, как модули runbook со скриптами PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. 

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Служба State Configuration службы автоматизации Azure предоставляет опрашивающий сервер DSC аналогично [компоненту Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Целевые узлы могут автоматически получать конфигурации, переходить в требуемое состояние и сообщать о соответствии. Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер. Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Служба State Configuration службы автоматизации Azure обеспечивает тот же слой управления для [Desired State Configuration PowerShell](/powershell/scripting/dsc/overview/overview), который служба автоматизации Azure предлагает для скриптов PowerShell. Вы можете управлять всеми конфигурациями, ресурсами и целевыми узлами DSC с помощью портала Azure или PowerShell.

![Снимок экрана со страницей службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Импорт данных отчетов в журналы Azure Monitor

Узлы, управление которыми осуществляется с помощью "Настройка состояния службы автоматизации Azure", отправляют подробные отчеты с данными о состоянии на встроенный опрашивающий сервер. В службе "Настройка состояния службы автоматизации Azure" можно настроить отправку этих данных в рабочую область Log Analytics. См. раздел [Пересылка данных отчетов службы State Configuration службы автоматизации Azure в журналах Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Требования для службы State Configuration службы автоматизации Azure

При использовании службы State Configuration службы автоматизации Azure учитывайте требования, описанные в этом разделе.

### <a name="operating-system-requirements"></a>Требования к операционной системе

Для узлов под управлением Windows поддерживаются следующие версии:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2;
- Windows Server 2012
- Windows Server 2008 R2 с пакетом обновления 1 (SP1)
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Номер SKU автономного продукта [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) не содержит реализацию DSC. Поэтому управлять им с помощью PowerShell DSC или службы State Configuration службы автоматизации Azure невозможно.

Для узлов под управлением Linux расширение DSC для Linux поддерживает все дистрибутивы Linux, перечисленные в разделе [Поддерживаемые дистрибутивы Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Требования DSC

Для всех узлов Windows, работающих в Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) устанавливается при включении компьютеров. Для узлов, работающих под управлением Windows Server 2012 и Windows 7, включен [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Для всех узлов Linux, работающих в Azure, [PowerShell DSC для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) устанавливается при включении компьютеров.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Настройка частных сетей

Если узлы находятся в частной сети, требуются следующие порты и URL-адреса. Эти ресурсы обеспечивают сетевое подключение для управляемого узла и позволяют DSC обмениваться данными со службой автоматизации Azure.

* Порт: только исходящий интернет-трафик через TCP-порт 443
* Глобальный URL-адрес: * **.azure-automation.net**
* Глобальный URL-адрес US Gov (Вирджиния): * **.azure automation.us**
* Служба агента: **https://\<ИД рабочей области\>.agentsvc.azure-automation.net**

При использовании ресурсов DSC, взаимодействующих между узлами, например [ресурсов WaitFor*](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), необходимо также разрешить трафик между узлами. Сведения об этих требованиях к сети см. в документации по каждому ресурсу DSC.

#### <a name="proxy-support"></a>Поддержка прокси-сервера

Поддержка прокси-сервера для агента DSC доступна в Windows версии 1809 и более поздних версиях. Этот параметр включается путем установки значений свойств `ProxyURL` и `ProxyCredential` в [скрипте метаконфигурации](automation-dsc-onboarding.md#generate-dsc-metaconfigurations), используемом для регистрации узлов. 

>[!NOTE]
>В службе State Configuration службы автоматизации Azure не предусмотрена поддержка прокси-сервера DSC для предыдущих версий Windows.

Для узлов Linux агент DSC поддерживает прокси-сервер и использует переменную `http_proxy` для определения URL-адреса. Дополнительные сведения о поддержке прокси-сервера см. в разделе [Создание метаконфигураций DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Диапазоны и пространство имен сети службы State Configuration службы автоматизации Azure

При определении исключений рекомендуется использовать адреса из списка ниже. Если вам нужны IP-адреса, вы можете скачать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

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
>Обновленный файл публикуется еженедельно. Он отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю. Скачивайте новый XML-файл каждую неделю и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. 

Пользователям Azure ExpressRoute следует обратить внимание, что этот файл используется для того, чтобы обновлять протокол BGP в пространстве Azure в первую неделю каждого месяца.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой State Configuration службы автоматизации Azure, см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о том, как включить узлы, см. в разделе [Включение службы State Configuration службы автоматизации Azure](automation-dsc-onboarding.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций DSC в службе State Configuration службы автоматизации Azure](automation-dsc-compile.md).
- Пример использования службы State Configuration в службе автоматизации Azure в конвейере непрерывного развертывания см. в разделе [Настройка непрерывного развертывания с помощью Chocolatey](automation-dsc-cd-chocolatey.md).
- Сведения о ценах см. на странице [с расценками для службы State Configuration службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).