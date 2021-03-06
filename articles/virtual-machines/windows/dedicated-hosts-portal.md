---
title: Развертывание выделенных узлов Azure с помощью портал Azure
description: Развертывание виртуальных машин на выделенных узлах с помощью портал Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 7abfa00b01f2e5e32b2486d69f36ac62f844c5ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033836"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Предварительная версия: развертывание виртуальных машин на выделенных узлах с помощью портала

В этой статье рассказывается, как создать [выделенный узел](dedicated-hosts.md) Azure для размещения виртуальных машин. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Выберите **Создать ресурс** в верхнем левом углу окна портала Azure.
1. На странице **Создать** в разделе **Популярные**выберите **Windows Server 2016 Datacenter**.
1. На вкладке " **основные** " в разделе " **сведения о проекте**" убедитесь, что выбрана правильная подписка, а затем выберите *мидедикатедхостсрг* в качестве **группы ресурсов**. 
1. В разделе **Подробности об экземпляре** введите *myVM* в поле **Имя виртуальной машины** и выберите *Восточная часть США* в поле **Расположение**.
1. В области **параметры доступности** выберите **зону доступности**и выберите *1* в раскрывающемся списке.
1. В поле Размер выберите **изменить размер**. В списке доступных размеров выберите один из рядов Esv3, например " **стандартный E2s v3**". Возможно, потребуется очистить фильтр, чтобы увидеть все доступные размеры.
1. В разделе **Учетная запись администратора** укажите имя пользователя, например *azureuser*, и пароль. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. В разделе **Правила входящего порта** щелкните **Разрешить выбранные порты**, а затем выберите **RDP (3389)** из раскрывающегося списка.
1. В верхней части страницы перейдите на вкладку **Дополнительно** и в разделе **узел** выберите *Михостграуп* для **группы узлов** и *михост* для **узла**. 
    ![выбрать группу узлов и](./media/dedicated-hosts-portal/advanced.png) узла
1. Оставьте остальные значения по умолчанию и нажмите кнопку **Просмотр и создание**, расположенную в нижней части страницы.
1. Когда появится сообщение о том, что проверка прошла успешно, выберите **создать**.


## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в разделе Общие сведения о [выделенных узлах](dedicated-hosts.md) . 

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)приведен пример шаблона, который использует зоны и домены сбоя для максимальной устойчивости в регионе.

- Кроме того, выделенный узел можно развернуть с помощью [Azure PowerShell](dedicated-hosts-powershell.md).