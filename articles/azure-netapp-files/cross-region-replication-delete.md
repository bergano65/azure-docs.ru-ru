---
title: Удаление репликации для Azure NetApp Files репликации между регионами | Документация Майкрософт
description: Описывает, как удалить подключение репликации, которое больше не требуется между исходным и конечным томами.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709054"
---
# <a name="delete-replications"></a>Удаление репликации

Можно завершить подключение репликации между исходным и целевым томами, удалив репликацию тома. Операцию удаления можно выполнить на исходном или целевом томе. Операция удаления удаляет только авторизацию для репликации; Он не удаляет исходный или целевой том. 

## <a name="steps"></a>Шаги

1. Чтобы удалить репликацию тома, выберите **репликацию** из исходного или целевого тома.  

2. Щелкните **Удалить**.    

3. Подтвердите удаление, введя **Да** и нажав кнопку **Удалить**.   

    ![Удаление репликации](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Дальнейшие действия  

* [Репликация между регионами](cross-region-replication-introduction.md)
* [Требования и рекомендации по использованию репликации между регионами](cross-region-replication-requirements-considerations.md)
* [Отображение состояния работоспособности отношения репликации](cross-region-replication-display-health-status.md)
* [Устранение неполадок при репликации между регионами](troubleshoot-cross-region-replication.md)

