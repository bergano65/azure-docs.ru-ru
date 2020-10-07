---
title: Краткое руководство. Включение службы "Автоматическое управление Azure для виртуальных машин" на портале Azure
description: Узнайте, как быстро включить Автоматическое управление Azure для виртуальных машин на новой или существующей виртуальной машине на портале Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: d66c19ce3a9786a5ca0f1390acb398c2a9cf502f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445799"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Краткое руководство. Включение службы "Автоматическое управление Azure для виртуальных машин" на портале Azure

С помощью Автоматического управления Azure для виртуальных машин на портале Azure можно включить автоматическое управление для новой или существующей виртуальной машины.


## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте учетную запись Azure](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), прежде чем начинать работу.

> [!NOTE]
> Учетные записи бесплатной пробной версии не предоставляют доступа к виртуальным машинам, которые используются в этом руководстве. Перейдите на подписку с оплатой по мере использования.

> [!IMPORTANT]
> Вы должны иметь роль **Участник**, чтобы включить Автоматическое управление с помощью существующей учетной записи Автоматического управления. Если вы включаете Автоматическое управление с помощью новой учетной записи, вам потребуются следующие разрешения: роль **Владелец** или **Участник** вместе с ролью **Администратор доступа пользователей**.


## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Включение Автоматического управления для виртуальных машин на существующей виртуальной машине

1. В строке поиска найдите и выберите **Automanage — Azure virtual machine best practices** (Автоматическое управление — Рекомендации для виртуальных машин Azure).

2. Выберите **Enable on existing VM** (Включить на существующей ВМ).

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Enable on existing VM"::: (Включить на существующей ВМ).

3. В колонке **Выбор компьютеров**:
    1. Отфильтруйте список виртуальных машин по полям **Подписка** и **Группа ресурсов**.
    1. Установите флажок для каждой виртуальной машины, которую требуется подключить.
    1. Нажмите кнопку **Выбрать**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Enable on existing VM":::

4. В разделе **Профиль конфигурации** щелкните **Browse and change profiles and preferences** (Найти и изменить профили и настройки).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Enable on existing VM":::

5. В колонке **Select configuration profile + preferences** (Выбор профиля конфигурации и настроек):
    1. Выберите профиль слева: *Разработка и тестирование* для тестирования, *Prod* (Производство) для производственных задач.
    1. Нажмите кнопку **Выбрать**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Enable on existing VM":::

6. Нажмите кнопку **Включить**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Включение Автоматического управления для виртуальных машин на новой виртуальной машине

Войдите на портал Azure [здесь](https://aka.ms/automanageportalnextstep), чтобы создать виртуальную машину и включить Автоматическое управление.

1. Выполните инструкции, описанные в статье [Краткое руководство. Создание виртуальной машины Windows с помощью портала Azure](..\virtual-machines\windows\quick-create-portal.md), чтобы создать ВМ.

2. После развертывания виртуальной машины внизу на странице состояния развертывания появятся рекомендуемые **дальнейшие действия**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Enable on existing VM":::

3. В разделе **Дальнейшие действия** выберите **Enable Automanage virtual machine best practices** (Включить рекомендации для Автоматического управления виртуальными машинами).

4. На странице **Automanage — Azure virtual machine best practices** (Автоматическое управление — Рекомендации для виртуальных машин Azure) поле **Компьютеры** автоматически заполнится созданной виртуальной машиной.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Enable on existing VM":::

5. В разделе **Профиль конфигурации** щелкните **Browse and change profiles and preferences** (Найти и изменить профили и настройки).

6. В колонке **Select configuration profile + preferences** (Выбор профиля конфигурации и настроек):
    1. Выберите профиль слева: *Разработка и тестирование* для тестирования, *Prod* (Производство) для производственных задач.
    1. Нажмите кнопку **Выбрать**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Enable on existing VM":::

7. Нажмите кнопку **Включить**.

## <a name="disable-automanage-for-vms"></a>Отключение Автоматического управления для виртуальных машин

Быстро остановить использование службы Автоматического управления Azure для виртуальных машин можно, отключив Автоматическое управление.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Enable on existing VM":::

1. Перейдите на страницу **Automanage — Azure virtual machine best practices** (Автоматическое управление — Рекомендации для виртуальных машин Azure), где перечислены все автоматически управляемые виртуальные машины.
1. Установите флажок рядом с виртуальной машиной, для которой необходимо отключить автоматическое управление.
1. Нажмите кнопку **Disable automanagent** (Отключить автоуправление).
1. Внимательно прочтите сообщения в появившемся всплывающем окне, прежде чем нажать кнопку **Отключить**.


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали новую группу ресурсов, чтобы попробовать Автоматическое управление Azure для виртуальных машин, и больше не нуждаетесь в ней, вы можете удалить эту группу ресурсов. Удаление группы также приведет к удалению виртуальной машины и всех ресурсов в группе ресурсов.

Автоматическое управление Azure создает группы ресурсов по умолчанию для хранения ресурсов. Убедитесь, что имена группы ресурсов соответствуют формату "DefaultResourceGroupRegionName" и "AzureBackupRGRegionName", чтобы можно было удалить все ресурсы.

1. Выберите пункт **Группа ресурсов**.
1. На странице этой группы ресурсов нажмите **Удалить**.
1. При появлении запроса подтвердите имя группы ресурсов, которую необходимо удалить, и нажмите **Удалить**.


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описывается включение Автоматического управления Azure для виртуальных машин. 

Узнайте, как можно создавать и применять пользовательские настройки при включении Автоматического управления на виртуальной машине. 

> [!div class="nextstepaction"]
> [Автоматическое управление Azure для виртуальных машин — настраиваемый профиль конфигурации](virtual-machines-custom-preferences.md)
