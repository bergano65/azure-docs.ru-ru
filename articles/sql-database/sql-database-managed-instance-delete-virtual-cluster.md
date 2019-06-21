---
title: Удалить подсеть после удаления базы данных SQL Azure управляемый экземпляр | Документация Майкрософт
description: Сведения об удалении виртуальной сети Azure после удаления базы данных SQL Azure управляемого экземпляра.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: ec5d99e160e739f59e2bf2ea369fe83e9900a1f1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295300"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Удалить подсеть после удаления базы данных SQL Azure управляемого экземпляра

Эта статья содержит рекомендации о том, как вручную удалить подсеть после удалении последней базы данных SQL управляемого экземпляра, находящихся в нем.

База данных SQL использует [виртуальный кластер](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) для хранения удаленных управляемого экземпляра. Виртуальный кластер сохраняются в течение 12 часов после удаления экземпляра, которые позволяют быстро создавать управляемые экземпляры в той же подсети. Не взимается плата для поддержания пустой виртуальный кластер. В течение этого периода не удается удалить подсеть, связанная с виртуальный кластер.

Если вы не хотите ждать 12 часов и хотите немедленно удалить виртуальный кластер и ее подсети, это можно сделать вручную. Удалите виртуальный кластер вручную с помощью портала Azure или виртуальные кластеры API.

> [!NOTE]
> Виртуальный кластер должен содержать управляемые экземпляры, не для удаления было успешным.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Удаление виртуального кластера на портале Azure

Чтобы удалить виртуальный кластер с помощью портала Azure, найдите ресурсы виртуального кластера.

![Снимок экрана с выделенным полем поиска на портале Azure](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

После того, виртуальный кластер, который требуется удалить, выберите этот ресурс и выберите **удалить**. Будет предложено подтвердить удаление виртуальный кластер.

![Снимок экрана портала Azure, виртуальных кластеров панели мониторинга, с выделенным параметром Delete](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

В области уведомления на портале Azure отображаются, подтверждение, что виртуальный кластер был удален. Виртуальный кластер успешного удаления немедленно освобождает подсети для повторного использования.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Удаление виртуального кластера с помощью API

Чтобы удалить виртуальный кластер через интерфейс API, используйте параметры URI, указанный в [метод delete виртуальные кластеры](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Использование Управляемого экземпляра Базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
- Узнайте, как [изменять имеющиеся виртуальные сети для Управляемого экземпляра](sql-database-managed-instance-configure-vnet-subnet.md).
- Чтобы узнать, как создать виртуальную сеть и Управляемый экземпляр, а также восстановить базу данных из резервной копии базы данных, ознакомьтесь со статьей [Краткое руководство. Создание Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-get-started.md).
- Во избежание проблем при настройке DNS ознакомьтесь со сведениями в статье [Настройка пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-custom-dns.md).
