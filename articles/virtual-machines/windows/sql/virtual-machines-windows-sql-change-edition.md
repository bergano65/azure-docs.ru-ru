---
title: Выполнение обновления на месте SQL Server выпуска на виртуальной машине Azure | Документация Майкрософт
description: Узнайте, как изменить выпуск SQL Server виртуальной машины в Azure.
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
ms.openlocfilehash: 04e447b7d8da1c8769239aee7650fe3bc5585590
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855232"
---
# <a name="perform-an-in-place-upgrade-of-a-sql-server-edition-on-an-azure-vm"></a>Выполнение обновления на месте SQL Server выпуска на виртуальной машине Azure

В этой статье описывается, как изменить выпуск SQL Server на виртуальной машине Windows в Azure. 

Выпуск SQL Server определяется ключом продукта и указывается в процессе установки. Выпуск определяет, какие [функции](/sql/sql-server/editions-and-components-of-sql-server-2017) доступны в SQL Serverном продукте. Вы можете изменить SQL Server Edition на установочный носитель и либо перейти к более ранней версии, чтобы сократить затраты, либо обновить ее, чтобы включить дополнительные функции.

Если вы обновили выпуск SQL Server с помощью установочного носителя после регистрации в поставщике ресурсов виртуальной машины SQL, то чтобы соответствующим образом обновить выставление счетов в Azure, необходимо задать свойство выпуска SQL Server для ресурса виртуальной машины SQL следующим образом:

1. Войдите на [портале Azure](https://portal.azure.com). 
1. Перейдите к ресурсу SQL Server виртуальной машины. 
1. В разделе **Параметры**выберите **настроить**. Затем выберите нужный выпуск SQL Server из раскрывающегося списка в разделе **выпуск**. 

   ![Изменение метаданных выпуска](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Обратите внимание на предупреждение о том, что сначала необходимо изменить выпуск SQL Server, а свойство Edition должно соответствовать выпуску SQL Server. 
1. Нажмите кнопку **Применить** , чтобы применить изменения метаданных выпуска. 


## <a name="prerequisites"></a>предварительные требования

Чтобы выполнить изменение выпуска SQL Server на месте, вам потребуется следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- [SQL Server виртуальную машину в Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) , зарегистрированную с помощью [поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Установочный носитель с нужным выпуском SQL Server. Клиенты, у которых есть [Программа Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) , могут получить установочный носитель из [центра корпоративного лицензирования](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Клиенты, у которых нет программы Software Assurance, могут использовать установочный носитель из Azure Marketplace SQL Server образ виртуальной машины с требуемым выпуском.


## <a name="upgrade-an-edition"></a>Обновление выпуска

> [!WARNING]
> Обновление выпуска SQL Server приведет к перезапуску службы для SQL Server, а также к любым связанным службам, таким как службы Analysis Services и R. 

Чтобы обновить SQL Server, получите установочный носитель SQL Server для нужного выпуска SQL Server, а затем выполните следующие действия.

1. Откройте файл Setup. exe с установочного носителя SQL Server. 
1. Перейдите в раздел **обслуживание** и выберите вариант **обновления выпуска** . 

   ![Выбор для обновления выпуска SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Выберите **Далее** , пока не появится страница **все готово для обновления выпуска** , а затем выберите **Обновить**. Пока изменение вступит в действие, окно установки может перестать отвечать на запросы в течение нескольких минут. На **полной** странице будет подтверждено, что обновление выпуска завершено. 

После обновления SQL Server выпуска измените свойство Edition SQL Serverной виртуальной машины в портал Azure, как показано выше. При этом будут обновлены метаданные и выставление счетов, связанные с этой виртуальной машиной.

## <a name="downgrade-an-edition"></a>Понижение выпуска

Чтобы понизить выпуск SQL Server, необходимо полностью удалить SQL Server и повторно установить его с помощью нужного носителя установки выпуска.

> [!WARNING]
> Удаление SQL Server может повлечь за собой дополнительное время простоя. 

Выпуск SQL Server можно понизить, выполнив следующие действия.

1. Резервное копирование всех баз данных, включая системные базы данных. 
1. Перемещение системных баз данных (Master, Model и msdb) в новое расположение. 
1. Полностью удалите SQL Server и все связанные службы. 
1. Перезапустите виртуальную машину. 
1. Установите SQL Server, используя носитель с нужным выпуском SQL Server.
1. Установите последние версии пакетов обновления и накопительные пакеты обновления.  
1. Замените новые системные базы данных, созданные во время установки, на системные базы данных, которые были ранее перемещены в другое расположение. 

После понижения SQL Server выпуска измените свойство Edition SQL Serverной виртуальной машины в портал Azure, как показано выше. При этом будут обновлены метаданные и выставление счетов, связанные с этой виртуальной машиной.

## <a name="remarks"></a>Примечания

- Свойство Edition для SQL Server виртуальной машины должно соответствовать выпуску экземпляра SQL Server, установленного для всех SQL Server виртуальных машин, включая типы лицензий с оплатой по мере использования и собственные лицензии.
- Если удалить ресурс виртуальной машины SQL Server, вернитесь к параметру жестко запрограммированный выпуск образа.
- Возможность изменения выпуска является компонентом поставщика ресурсов виртуальной машины SQL. Развертывание образа Azure Marketplace с помощью портал Azure автоматически регистрирует SQL Server виртуальную машину с помощью поставщика ресурсов. Тем не менее клиенты, которые самостоятельно устанавливают SQL Server, должны вручную [зарегистрировать свои виртуальные машины SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- Для добавления SQL Server виртуальной машины в группу доступности необходимо повторно создать виртуальную машину. Все виртуальные машины, добавленные в группу доступности, будут возвращаться к выпуску по умолчанию, а выпуск потребуется изменить снова.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Часто задаваемые вопросы о SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценам для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


