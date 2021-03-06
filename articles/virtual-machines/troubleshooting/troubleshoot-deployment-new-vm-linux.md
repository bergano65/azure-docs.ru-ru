---
title: Устранение неполадок развертывания виртуальных машин Linux | Документация Майкрософт
description: Устранение неполадок развертывания Resource Manager при создании виртуальной машины Linux в Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: f02e1f73460140c9fe9f2cf6d7ffda26533d570d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090024"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Устранение неполадок в развертывании Resource Manager при создании виртуальной машины Linux в Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Наиболее важные проблемы
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Описание других ситуаций и вопросов, связанных с развертыванием виртуальных машин, см. в статье [Устранение неполадок при развертывании виртуальных машин Linux в Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Сбор журналов действий
Для устранения неполадок прежде всего соберите журналы действий, чтобы определить ошибку, связанную с этой проблемой. Ниже представлены ссылки на подробные инструкции.

[Просмотр операций развертывания с помощью Azure Resource Manager](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Просмотр журналов действий для управления ресурсами Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y.** Если ОС является универсальной для Linux, а затем она передается и (или) захватывается с помощью обобщенного параметра, то ошибок не будет. Аналогичным образом, если специализированная ОС Linux передается и/или записывается как специализированный диск, ошибок не возникает.

**Ошибки передачи:**

**N<sup>1</sup>.** Если ОС является универсальной для Linux и она передается как специализированная, вы получите ошибку времени ожидания подготовки, так как виртуальная машина зависает на этапе подготовки.

**N<sup>2</sup>.** Если ОС является специализированной операционной системой Linux и она передается как обобщенная, будет выдано сообщение об ошибке подготовки, так как новая виртуальная машина выполняется с исходным именем компьютера, имя пользователя и пароль.

**Способы устранения:**

Чтобы устранить обе ошибки, отправьте исходный виртуальный жесткий диск, доступный локально, с тем же параметром, что и для ОС (обобщенный или специализированный). Чтобы передать диск как универсальный, сначала обязательно выполните команду -deprovision.

**Ошибки записи:**

**N<sup>3</sup>.** Если ОС является универсальной для Linux и она записывается как специализированная, вы получите ошибку времени ожидания подготовки, так как исходная виртуальная машина не может использоваться, так как она помечена как обобщенная.

**N<sup>4</sup>.** Если ОС является специализированной операционной системой Linux и она записывается как обобщенная, вы получите сообщение об ошибке подготовки, так как новая виртуальная машина выполняется с исходным именем компьютера, имя пользователя и пароль. Кроме того, исходная виртуальная машина отмечена как специализированная и непригодна к использованию.

**Способы устранения:**

Чтобы устранить обе ошибки, удалите на портале текущий образ и [заново запишите его с текущих виртуальных жестких дисков](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с тем же параметром (универсальный или специализированный), который установлен для операционной системы.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Проблема Пользовательский образ, изображение из галереи или в Marketplace; сбой выделения
Данная ошибка возникает в ситуациях, когда запрос на новую виртуальную машину прикреплен к кластеру, который либо не поддерживает запрашиваемый размер виртуальной машины, либо не располагает свободным пространством для размещения запроса.

**Причина 1**. Кластер не поддерживает запрошенный размер виртуальной машины.

**Способ устранения 1.**

* Повторите запрос с указанием меньшего размера виртуальной машины.
* Если нельзя изменить размер запрошенной виртуальной машины,
  * остановите все виртуальные машины в группе доступности.
    Для этого последовательно выберите **Группы ресурсов** > *имя вашей группы ресурсов* > **Ресурсы** > *имя вашей группы доступности* > **Виртуальные машины** > *имя вашей виртуальной машины* > **Остановить**.
  * После остановки всех виртуальных машин создайте новую виртуальную машину необходимого размера.
  * Сначала запустите новую виртуальную машину, а затем выберите каждую из остановленных виртуальных машин и нажмите кнопку **Запустить**.

**Причина 2.** В кластере нет свободных ресурсов.

**Способ устранения 2.**

* Повторите запрос позже.
* Если новая виртуальная машина должна быть частью другой группы доступности:
  * создайте новую виртуальную машину в другой группе доступности (в том же регионе);
  * добавьте новую виртуальную машину в ту же виртуальную сеть.

## <a name="next-steps"></a>Следующие шаги
При возникновении проблем во время запуска остановленной виртуальной машины Linux или в случае изменения размера существующей виртуальной машины Linux в Azure см. раздел [Устранение неполадок в развертывании Resource Manager при перезагрузке или изменении размера существующей виртуальной машины Linux в Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

