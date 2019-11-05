---
title: Изменение представления сеанса виртуальной машины на весь экран в Azure бастиона | Документация Майкрософт
description: Из этой статьи вы узнаете, как изменить представление на полноэкранный.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: a8c9cab825644fe797713c80d3a710c7a2d54850
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498050"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Переход в полноэкранный режим для сеанса виртуальной машины: Azure бастиона

Эта статья поможет вам изменить представление виртуальной машины на полноэкранное и обратно в браузере. Перед началом работы с виртуальной машиной убедитесь, что выполнены действия по [созданию узла бастиона](bastion-create-host-portal.md). Затем подключитесь к виртуальной машине, которую вы хотите использовать, с помощью [RDP](bastion-connect-vm-rdp.md) или [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Запуск средства буфера обмена

В ходе удаленного сеанса откройте палитру средств доступа к буферу обмена бастиона, выбрав две стрелки, расположенные в левом центре сеанса.

![средства](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Выберите "во весь экран"

Нажмите кнопку **полноэкранного** режима, чтобы перевести сеанс в полноэкранный режим. После переключения сеанс повторно инициализируется до полного экрана.

![во весь экран](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [часто задаваемыми вопросами о бастиона](bastion-faq.md).
Сведения о [копировании и вставке](bastion-vm-copy-paste.md) на виртуальную машину Azure и из нее.