---
title: включение файла
description: включение файла
services: machine-learning
author: j-martens
ms.service: machine-learning
ms.author: jmartens
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: fb874d06fbccc7e1db542a5d063b9b6acd35b83f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240122"
---
>[!IMPORTANT]
>Созданные ресурсы могут использоваться в качестве необходимых компонентов для других руководств и статей с практическими рекомендациями по Службе машинного обучения Azure. 


Если вы не собираетесь использовать ресурсы, созданные при работе с этим кратким руководством, удалите их, чтобы не платить за их использование.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.  
 
   ![Удаление на портале Azure](./media/aml-delete-resource-group/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов, а затем нажмите кнопку **Удалить**.

   Появится ошибка "Не удается удалить ресурс до удаления вложенных ресурсов". Вам нужно удалить эти вложенные ресурсы. См. дополнительные сведения об [устранении неполадок](../articles/machine-learning/desktop-workbench/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account). 
