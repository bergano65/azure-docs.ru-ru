---
title: Расширения и компоненты виртуальной машины Azure | Документы Майкрософт
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
ms.openlocfilehash: deb49267a262705370e48e150cc5ed6c4dc04247
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168892"
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

Сведения об устранении неполадок для каждого расширения можно найти в разделе **Устранение неполадок и поддержка** в обзоре расширения. Ниже приведен список доступных сведений по устранению неполадок.

| Пространство имен | Устранение неполадок |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. депенденциажент. депенденциажентлинукс | [Зависимость от Azure Monitor для Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. депенденциажент. депенденциажентвиндовс | [Зависимость Azure Monitor для Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Шифрование дисков Azure для Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Шифрование дисков Azure для Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. COMPUTE. customscriptextension | [Пользовательский скрипт для Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Настройка требуемого состояния для Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Настройка требуемого состояния для Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. хпккомпуте. нвидиагпудриверлинукс | [Расширение драйвера GPU NVIDIA для Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. хпккомпуте. нвидиагпудривервиндовс | [Расширение драйвера GPU NVIDIA для Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Расширение антивредоносной программы для Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. ентерприсеклауд. Monitoring. omsagentforlinux | [Azure Monitor для Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. ентерприсеклауд. Monitoring. расширение microsoftmonitoringagent | [Azure Monitor для Windows](oms-windows.md#troubleshoot-and-support) |
| stackify. линуксажент. extension. стаккифилинуксажентекстенсион | [Перетрассировка Stackify для Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Сброс пароля (VMAccess) для Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. vmsnapshot | [Моментальный снимок для Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. vmsnapshot | [Моментальный снимок для Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения о работе агента и расширений для Linux см. в статье о [расширениях и компонентах виртуальной машины Azure для Linux](features-linux.md).
* Дополнительные сведения о работе гостевого агента и расширений для Windows см. в статье о [расширениях и компонентах виртуальной машины Azure для Windows](features-windows.md).  
* Сведения об установке гостевого агента Windows см. в статье [Обзор агента виртуальных машин Windows в Azure](agent-windows.md).  
* Сведения об установке агента Linux см. в статье [Обзор агента виртуальной машины Linux в Azure](agent-linux.md).  

