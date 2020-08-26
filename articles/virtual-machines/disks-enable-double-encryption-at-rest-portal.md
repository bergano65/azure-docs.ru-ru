---
title: Включение двойного шифрования на дисках, управляемых портал Azureами неактивных дисков
description: Включите двойное шифрование неактивных данных для управляемых дисков с помощью портал Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bf2531536796965f145a9ac3e6a23cbb6634852a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817921"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Использование портал Azure для включения двойного шифрования при хранении для управляемых дисков

Хранилище дисков Azure поддерживает двойное шифрование при хранении для управляемых дисков. Концептуальные сведения о двойном шифровании неактивных данных и других типах шифрования дисков см. в разделе **двойное шифрование при** хранении статьи о шифровании диска:

- Для Linux: [двойное шифрование неактивных](./linux/disk-encryption.md#double-encryption-at-rest)
- Для Windows: [двойное шифрование неактивных](./windows/disk-encryption.md#double-encryption-at-rest) значений

## <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Начало работы

1. Войдите на [портал Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Для доступа к портал Azure необходимо использовать [предоставленную ссылку](https://aka.ms/diskencryptionupdates) . Двойное шифрование неактивных компонентов в настоящее время не отображается в общедоступной портал Azure без использования ссылки.

1. Найдите и выберите **наборы шифрования дисков**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Снимок экрана основного портал Azure. наборы шифрования диска выделены на панели поиска.":::

1. Щелкните **+ Добавить**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Снимок экрана: колонка набор шифрования диска, "+ Добавить".":::

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
    
    ![Снимок экрана с успешными разрешениями и назначением ролей для хранилища ключей.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Перейдите к диску.
1. Выберите **Шифрование**.
1. В качестве **типа шифрования**выберите **двойное шифрование с управляемыми платформой ключами и управляемыми клиентом ключи**.
1. Выберите набор шифрования дисков.
1. Щелкните **Save** (Сохранить).

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Снимок экрана колонки шифрование для управляемого диска выделено упомянутый тип шифрования.":::

Теперь вы включили двойное шифрование неактивных на управляемом диске.


## <a name="next-steps"></a>Дальнейшие действия

- [Azure PowerShell — включить управляемые клиентом ключи с помощью управляемых дисков с шифрованием на стороне сервера](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Примеры шаблонов Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Использование управляемых клиентом ключей с шифрованием на стороне сервера. примеры](./linux/disks-enable-customer-managed-keys-cli.md#examples)
