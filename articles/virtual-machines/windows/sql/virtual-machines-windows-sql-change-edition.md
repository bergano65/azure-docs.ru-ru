---
title: Как выполнить обновление на месте выпуска SQL Server на виртуальной Машине Azure | Документация Майкрософт
description: Узнайте, как изменения выпуска виртуальной Машине SQL Server в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607550"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Как выполнить обновление на месте выпуска SQL Server на виртуальной Машине Azure

В этой статье описывается, как для изменения выпуска SQL Server на существующем сервере SQL, на виртуальной машине Windows в Azure. 

Выпуск SQL Server определяется с помощью ключа продукта и указывается с помощью процесса установки. Определяет выпуск, что [функции](/sql/sql-server/editions-and-components-of-sql-server-2017) доступны в пределах продукта SQL Server. Вы можете изменить выпуск SQL Server с установочного носителя и на любом более раннюю версию снизить затраты или обновление, чтобы включить дополнительные функции.

Если вы обновили выпуск SQL Server с помощью установочного носителя после регистрации в поставщике ресурсов виртуальной Машины SQL, затем для обновления Azure соответствующим образом счета следует установить SQL Server Edition свойство ресурсов виртуальной Машины SQL следующим образом:

1. Войдите на [портал Azure](https://portal.azure.com). 
1. Перейдите к ресурсу виртуальной машины SQL Server. 
1. В разделе **параметры**выберите **Настройка** и выберите из раскрывающегося списка на нужный выпуск SQL Server **Edition**. 

   ![Изменение метаданных edition](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Ознакомьтесь с предупреждением, уведомлением о том, что сначала необходимо изменить выпуск SQL Server, и это свойство edition должен совпадать выпуск SQL Server. 
1. Выберите **применить** для применения изменений метаданных edition. 


## <a name="prerequisites"></a>предварительные требования

Чтобы сделать на месте выпуска SQL Server, необходимо следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- Windows [виртуальной Машины SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) зарегистрировано [поставщика ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Установочный носитель с нужный выпуск SQL Server. Клиенты, имеющие [программы software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) можно получить их установочного носителя из [центр корпоративного лицензирования](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Клиенты не имеют software assurance можно использовать установочный носитель из образа marketplace виртуальной Машины SQL Server с их нужный выпуск.


## <a name="upgrade-edition"></a>Обновления выпуска

  > [!WARNING]
  > - **Обновление выпуска SQL Server будет перезапущена служба для SQL Server, а также все связанные службы, такие как службы Analysis Services и R Services.** 

Обновление выпуска SQL Server, получить установочный носитель SQL Server для нужный выпуск SQL Server и затем сделайте следующее:

1. Запустите Setup.exe с установочного носителя SQL Server. 
1. Перейдите к **обслуживания** и выберите **обновление выпуска** параметр. 

   ![Обновление выпуска SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Выберите **Далее** пока не достигнете **все готово для обновления выпуска** странице, а затем выберите **обновление**. Изменение вступает в силу, а затем появится окно установки может зависнуть в течение нескольких минут **завершить** страница с подтверждением, что обновление выпуска завершена. 

После обновления выпуска SQL Server, следует изменять свойство «Выпуска» из виртуальной машины Sql на портале Azure, как показано выше; Это обновит метаданных и выставление счетов, связанных с этой виртуальной Машины.

## <a name="downgrade-edition"></a>Переход на более ранний выпуск

  > [!WARNING]
  > - **Понижение уровня выпуска SQL Server требуется полностью удалить SQL Server, это может повлечь дополнительное время простоя возникнет**. 


Чтобы понизить версию SQL Server, необходимо полностью удалить SQL Server и повторно установить его с установочного носителя нужный выпуск. 

Понизить версию выпуск SQL Server можно, выполнив следующие действия:

1. Резервное копирование всех баз данных, включая системные базы данных. 
1. Перемещение системных баз данных (master, model и msdb) в новое расположение. 
1. Следует полностью удалите SQL Server и все связанные службы. 
1. Перезапустите виртуальную машину. 
1. Установка SQL Server, используя носитель с нужный выпуск SQL Server.
1. Установите последние пакеты обновления и накопительные пакеты обновления.  
1. Замените новые системные базы данных, которые были созданы во время установки с системными базами данных, ранее перемещенные в другое расположение. 

После выпуска SQL Server восстановлена предыдущая версия, следует изменять свойство «Выпуска» виртуальной машины SQL на портале Azure, как показано выше; Это обновит метаданных и выставление счетов, связанных с этой виртуальной Машины.

## <a name="remarks"></a>Примечания

 - Свойство выпуска для виртуальной Машины SQL Server, должно соответствовать выпуск SQL Server, к виртуальной машине установлены для всех виртуальных машин SQL включая PAYG и BYOL типы лицензий.
 - Если удалить ресурс виртуальной Машины SQL Server, будет выполнен переход обратно параметр жестко выпуска образа.
  - Возможность изменения выпуска — это функция поставщика ресурсов виртуальной Машины SQL. Развертывание образа marketplace на портале Azure автоматически регистрирует виртуальную Машину SQL Server с поставщиком ресурсов. Тем не менее клиенты, которые самостоятельно при установке SQL Server потребуется вручную [зарегистрировать их виртуальной Машине SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- Добавление виртуальной Машины SQL Server в группу доступности требует повторного создания ВМ. Как такие, все виртуальные машины, добавление в доступности набора вернется к выпуск по умолчанию и выпуск потребуется снова изменяется.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).


