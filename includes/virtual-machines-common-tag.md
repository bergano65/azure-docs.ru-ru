---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67185227"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Пометка виртуальной машины с помощью шаблонов
Для начала рассмотрим пометку с помощью шаблонов. [Этот шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) размещает теги в следующих ресурсах: Compute (виртуальная машина), хранилище (учетная запись хранения) и сеть (общедоступный IP-адрес, виртуальная сеть и сетевой интерфейс). (Это шаблон для виртуальной машины Windows, но его можно приспособить для виртуальных машин Linux.)

Нажмите кнопку **Развернуть в Azure** , после того как перейдете по [ссылке на шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Откроется [портал Azure](https://portal.azure.com/) , где можно развернуть этот шаблон.

![Простое развертывание с тегами](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Этот шаблон включает следующие Теги: *Отдел*, *приложение*и *созданный*. Вы можете добавлять и редактировать эти теги непосредственно в шаблоне, если вам нужно изменить их имена.

![Теги Azure в шаблоне](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Как видите, теги определяются как пары "ключ-значение", разделенные двоеточием (:). Теги должны быть определены в следующем формате:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Завершив добавление нужных тегов в файл шаблона, сохраните его.

Затем вы можете указать значения для тегов в разделе **Редактирование параметров** .

![Редактирование тегов на портале Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Нажмите **Создать** , чтобы развернуть шаблон со значениями тегов.

## <a name="tagging-through-the-portal"></a>Пометка с помощью портала
После создания ресурсов с тегами вы можете просматривать, добавлять и удалять теги на портале.

Выберите значок тегов, чтобы просмотреть свои теги:

![Значок тегов на портале Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Добавьте новый тег с помощью портала, определив собственную пару "ключ-значение", и сохраните его.

![Добавление тега на портале Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Новый тег должен появиться в списке тегов для ресурса.

![Новый тег, сохраненный на портале Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

