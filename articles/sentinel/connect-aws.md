---
title: Подключайте данные из Symantec AWS предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данных Symantec AWS.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673977"
---
# <a name="connect-azure-sentinel-to-aws"></a>Подключение Azure Sentinel к AWS

Используйте соединитель AWS для потоковой передачи все события CloudTrail в AWS в Azure Sentinel. Этот процесс подключения делегирует доступ для Azure Sentinel журналов ресурсов AWS, создание отношений доверия между CloudTrail в AWS и Azure Sentinel. Для этого в AWS, создав роли, которая предоставляет разрешение для Azure Sentinel для доступа к журналам AWS.

## <a name="prerequisites"></a>предварительные требования

Необходимо иметь разрешение на запись в рабочей области Azure Sentinel.

## <a name="connect-aws"></a>Подключение AWS 
 
1.  В вашей Amazon Web Services консоли, в разделе **безопасность, удостоверения и соответствие требованиям**, щелкните **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Выберите **ролей** и нажмите кнопку **Создание ролей**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Выберите **AWS другой учетной записи.** В **идентификатор учетной записи** введите **идентификатор учетной записи Майкрософт** (**123412341234**), можно найти на странице соединителя AWS на портале Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Убедитесь, что **требуется внешний идентификатор** выбран и затем выберите и введите внешний идентификатор (идентификатор рабочей области), который можно найти на странице соединителя AWS на портале Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  В разделе **применена эта политика разрешения** выберите **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Введите тег (необязательно).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Затем введите **имя роли** и нажмите кнопку **Создание ролей** кнопки.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  В списке ролей выберите роль, которую вы создали.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Копировать **ARN роли** и вставьте его в **роль для добавления** поле на портале Azure Sentinel.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Для соответствующей схемы в Log Analytics можно использовать для событий AWS, поиск **AWSCloudTrail**.



## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключиться к Azure Sentinel CloudTrail в AWS. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

