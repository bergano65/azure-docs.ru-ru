---
title: Расширения и компоненты виртуальной машины Azure
description: Сведения о расширениях виртуальной машины Azure и их использовании на виртуальных машинах Azure.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072979"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Расширения и компоненты виртуальной машины Azure
Расширения виртуальных машин Azure — это небольшие приложения для выполнения задач настройки и автоматизации после развертывания на виртуальных машинах Azure. Вы можете использовать существующие образы и затем настраивать их в рамках развертываний, не прибегая к процедуре создания настраиваемых образов.

На платформе Azure доступно множество расширений, в число которых входят приложения для конфигурации, мониторинга, безопасности виртуальных машин, а также служебные приложения. Издатели берут приложение, помещают его в расширение и упрощают установку, поэтому вам нужно только указать обязательные параметры. 

 Существует большое количество основных и сторонних расширений. Если приложение отсутствует в репозитории расширений, можно воспользоваться расширением настраиваемых скриптов и настроить виртуальную машину с помощью собственных скриптов и команд.

Вот некоторые основные варианты использования расширений:
* Настройка виртуальной машины. Можно использовать расширения DSC PowerShell (Desired State Configuration), Chef, Puppet и расширения настраиваемых скриптов для установки агентов конфигурации виртуальной машины и настройки виртуальной машины. 
* Продукты AV, такие как Symantec, ESET.
* Средства управления уязвимостями виртуальных машин от таких компаний, как Qualys, Rapid7, HPE.
* Средства мониторинга приложений и виртуальных машин, такие как DynaTrace, Наблюдатель за сетями Azure, Site24x7 и Stackify.

Расширения могут идти в комплекте с новым развертыванием виртуальной машины. Например, они могут входить в состав более крупного развертывания и использоваться для настройки приложений при подготовке виртуальной машины или выполняться в поддерживаемых операционных системах после развертывания.

## <a name="how-can-i-find-what-extensions-are-available"></a>Как узнать, какие расширения доступны?
Доступные расширения можно просмотреть на портале в колонке виртуальной машины в разделе расширений, однако там представлена лишь небольшая их часть. Чтобы увидеть полный список, воспользуйтесь средствами интерфейса командной строки. Дополнительные сведения см. в разделах о [поиске расширений ВМ для Linux](features-linux.md) и [поиске расширений ВМ для Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Как установить расширение?
Расширениями виртуальной машины Azure можно управлять с помощью Azure CLI, Azure PowerShell, шаблонов Azure Resource Manager или портала Azure. Чтобы опробовать расширение, перейдите на портал Azure, выберите расширение настраиваемых скриптов, затем передайте команду или скрипт и запустите расширение.

Если вам необходимо то же разрешение, добавленное на портале с помощью CLI или шаблона Resource Manager, см. документацию к другому расширению, например к [расширению настраиваемых скриптов для Windows](custom-script-windows.md) и [расширению настраиваемых скриптов для Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Как осуществляется управление жизненным циклом приложений расширений?
Для установки или удаления расширения не требуется подключаться к виртуальной машине напрямую. Так как управление жизненным циклом приложений расширений Azure осуществляется за пределами виртуальной машины, а сам жизненный цикл интегрирован в платформу Azure, вы можете получить сведения о состоянии расширения в целом.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Что еще следует знать о расширениях?
Расширения устанавливают приложения, поэтому для них существуют определенные требования. В частности, следует принимать во внимание поддерживаемые операционные системы Windows и Linux и обязательно устанавливать агенты виртуальных машин Azure. Для отдельных приложений расширений виртуальных машин могут действовать собственные предварительные требования, например доступ к конечной точке.

## <a name="troubleshoot-extensions"></a>Устранение неполадок расширений

Информация о проблемно-схватом для каждого расширения может быть найдена в разделе **Troubleshoot и поддержки** в обзоре для расширения. Вот список информации о устранении неполадок доступны:

| Пространство имен | Устранение неполадок |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Зависимость от монитора Azure для Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagent.dependencyagentwindows | [Зависимость от монитора Azure для Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Лазурное шифрование дисков для Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Шифрование дисков Azure для Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Пользовательский скрипт для Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Пожеланная конфигурация состояния для Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Пожеланная конфигурация состояния для Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA GPU Расширение драйвера для Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [NVIDIA GPU Расширение драйвера для Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Антивредоносное расширение для Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor для Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.Microsoftmonitoringagent | [Монитор Azure для Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Проследить стеки для Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Сбросить пароль (VMAccess) для Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Снимок для Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Снимок для Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о работе агента и расширений для Linux см. в статье о [расширениях и компонентах виртуальной машины Azure для Linux](features-linux.md).
* Дополнительные сведения о работе гостевого агента и расширений для Windows см. в статье о [расширениях и компонентах виртуальной машины Azure для Windows](features-windows.md).  
* Чтобы установить гостевой [Azure Windows Virtual Machine Agent Overview](agent-windows.md)агент Windows, см.  
* Чтобы установить Linux [Azure Linux Virtual Machine Agent Overview](agent-linux.md)Agent, см.  

