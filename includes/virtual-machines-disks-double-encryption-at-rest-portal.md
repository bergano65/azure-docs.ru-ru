---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177054"
---
## <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Начало работы

1. Войдите на [портал Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Для доступа к портал Azure необходимо использовать [предоставленную ссылку](https://aka.ms/diskencryptionupdates) . Двойное шифрование неактивных компонентов в настоящее время не отображается в общедоступной портал Azure без использования ссылки.

1. Найдите и выберите **наборы шифрования дисков**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Снимок экрана основного портал Azure. наборы шифрования диска выделены на панели поиска.":::

1. Щелкните **+ Добавить**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Снимок экрана: колонка "набор шифрования диска", "+ Добавить".":::

1. Выберите один из поддерживаемых регионов.
1. В качестве **типа шифрования**выберите **двойное шифрование с управляемыми платформой ключами и управляемыми клиентом ключи**.

    > [!NOTE]
    > После создания набора шифрования диска с определенным типом шифрования его нельзя изменить. Если вы хотите использовать другой тип шифрования, необходимо создать новый набор шифрования дисков.

1. Заполните оставшуюся информацию.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Снимок экрана: колонка создания набора шифрования дисков, регионы и двойное шифрование с управляемыми платформой ключами и управляемыми клиентом сочетаниями.":::

1. Выберите Azure Key Vault и ключ или создайте новый при необходимости.

    > [!NOTE]
    > При создании экземпляра Key Vault необходимо включить защиту с обратимым удалением и очисткой. Эти параметры являются обязательными при использовании Key Vault для шифрования управляемых дисков, а также для защиты от потери данных из-за случайного удаления.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Снимок экрана: колонка создания Key Vault.":::

1. Нажмите кнопку **создания**.
1. Перейдите к созданному набору шифрования дисков и выберите отображаемую ошибку. При этом для шифрования дисков будет настроено значение работает.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Снимок экрана с сообщением об ошибке: отображаемый код ошибки. сообщение об ошибке. чтобы связать диск, изображение или моментальный снимок с этим набором шифрования диска, необходимо предоставить разрешения для хранилища ключей.":::

    Уведомление должно быть всплывающим и успешных. Это позволит использовать шифрование дисков, установленное в хранилище ключей.
    
    ![Снимок экрана с успешными разрешениями и назначением ролей для хранилища ключей.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Перейдите к диску.
1. Выберите **Шифрование**.
1. В качестве **типа шифрования**выберите **двойное шифрование с управляемыми платформой ключами и управляемыми клиентом ключи**.
1. Выберите набор шифрования дисков.
1. Щелкните **Save** (Сохранить).

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Снимок экрана колонки "шифрование" для управляемого диска выделено упомянутый тип шифрования.":::

Теперь вы включили двойное шифрование неактивных на управляемом диске.
