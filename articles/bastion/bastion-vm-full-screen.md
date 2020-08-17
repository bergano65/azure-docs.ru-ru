---
title: 'Azure бастиона: Просмотр сеанса виртуальной машины: во весь экран'
description: Узнайте, как изменить представление виртуальной машины на полноэкранное и обратно в браузере для подключения RDP или SSH в Azure бастиона.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: fa795d7b066733aba54f8612bbabecf264a11c70
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270297"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Переход в полноэкранный режим для сеанса виртуальной машины: Azure бастиона

Эта статья поможет вам изменить представление виртуальной машины на полноэкранное и обратно в браузере. Перед началом работы с виртуальной машиной убедитесь, что выполнены действия по [созданию узла бастиона](bastion-create-host-portal.md). Затем подключитесь к виртуальной машине, которую вы хотите использовать, с помощью [RDP](bastion-connect-vm-rdp.md) или [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Запуск средства буфера обмена

В ходе удаленного сеанса откройте палитру средств доступа к буферу обмена бастиона, выбрав две стрелки, расположенные в левом центре сеанса.

![средства](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Выберите "во весь экран"

Нажмите кнопку **полноэкранного** режима, чтобы перевести сеанс в полноэкранный режим. После переключения сеанс повторно инициализируется до полного экрана.

![полноэкранный режим](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [часто задаваемыми вопросами о Бастионе Azure](bastion-faq.md).
Сведения о [копировании и вставке](bastion-vm-copy-paste.md) на виртуальную машину Azure и из нее.
