---
title: Интеграция Key Vault с SQL Server на виртуальных машинах Windows в Azure (Resource Manager) | Документация Майкрософт
description: Узнайте, как автоматизировать настройку шифрования SQL Server для использования с хранилищем ключей Azure. В этом разделе описываются способы использования интеграции хранилища ключей Azure с виртуальными машинами SQL Server, созданными с помощью диспетчера ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 13d698cfbc0241248a77fd5f3b148a9393320c64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076013"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Настройка интеграции Azure Key Vault для SQL Server на виртуальных машинах Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Обзор
Существует несколько функций шифрования SQL Server, например [прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) и [шифрование резервной копии](https://msdn.microsoft.com/library/dn449489.aspx). Эти формы шифрования требуют хранить используемые для шифрования ключи и управлять ими. Хранилище ключей Azure (AKV) предназначено для повышения безопасности и управления этими ключами в расположении высокой надежности и безопасности. [Соединитель SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) позволяет SQL Server использовать эти ключи из хранилища ключей Azure.

Если вы используете SQL Server с локальными машинами, выполните [шаги для доступа к хранилищу ключей Azure с локальной виртуальной машины SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Однако в случае SQL Server на виртуальных машинах Azure можно сэкономить время, воспользовавшись функцией *интеграции хранилища ключей Azure* .

Если эта функция включена, она автоматически устанавливает соединитель SQL Server, настраивает поставщик расширенного управления ключами для доступа к хранилищу ключей Azure и создает учетные данные, которые позволяют получить доступ к вашему хранилищу. Если вы изучили шаги в ранее упомянутой документации для локального компьютера, то увидели, что эта функция автоматизирует шаги 2 и 3. Единственное, что останется сделать вручную, — создать хранилище ключей и ключи. Далее вся настройка виртуальной машины SQL выполняется автоматически. После завершения установки этой функции можно выполнять инструкции T-SQL, чтобы начать шифрование базы данных или резервное копирование как обычно.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Поставщик расширенного управления ключами (EKM) версии 1.0.4.0 устанавливается на виртуальной машине SQL Server с помощью [расширения SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). При обновлении расширения SQL IaaS версия поставщика не обновляется. При необходимости вручную обновите версию поставщика EKM (например, во время миграции на Управляемый экземпляр Базы данных SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Включение и настройка интеграции AKV
Интеграцию AKV можно включить во время подготовки или настроить ее для существующих виртуальных машин.

### <a name="new-vms"></a>Новые виртуальные машины
При подготовке новой виртуальной машины SQL Server с помощью Resource Manager портал Azure дает возможность включить интеграцию Azure Key Vault. Функции Azure Key Vault доступны только для выпусков SQL Server Enterprise, Developer и Evaluation.

![Интеграция SQL с хранилищем ключей Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Подробное пошаговое руководство по подготовке, см. в разделе [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Для существующих виртуальных машин SQL Server, откройте ваш [ресурсов виртуальных машин SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) и выберите **безопасности** под **параметры**. Выберите **включить** для включения интеграции хранилища ключей Azure. 

![Интеграция AKV SQL для существующих виртуальных машин](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Когда закончите, нажмите **применить** кнопки в нижней части **безопасности** страницы, чтобы сохранить изменения.

> [!NOTE]
> Имя учетных данных, который мы создали здесь будет сопоставлено имя входа SQL более поздней версии. С помощью этих данных для входа в SQL можно получить доступ к хранилищу ключей. 


> [!NOTE]
> Можно также настроить интеграцию AKV с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для интеграции с хранилищем ключей Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
