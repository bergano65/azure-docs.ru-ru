---
title: Проверка базы данных SQL Azure за брандмауэром
description: Узнайте, как сканировать ресурсы за брандмауэром с помощью портала Azure зрения.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553812"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Проверка базы данных SQL Azure за брандмауэром в Azure зрения

Из этой статьи вы узнаете, как использовать Azure зрения для сканирования ресурса за брандмауэром.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

* Собственный [клиент Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com) с помощью своей учетной записи Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Настройка хранилища SQL за брандмауэром

Первым шагом является добавление MSI-файла каталога в базу данных SQL Azure с помощью [регистрации и сканирование базы данных SQL Azure](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Проверка базы данных SQL с зрения

1. Перейдите на домашнюю страницу зрения.

1. В области навигации слева выберите **центр управления** .

1. Выберите **Источники данных** в разделе " **источники и сканирование**".

1. Выберите **+ создать** , чтобы добавить источник данных.

1. Выберите **База данных SQL Azure**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Снимок экрана для выбора SQL Server.":::

1. Укажите имя для нового источника данных, выберите **из подписки Azure**, а затем выберите подписку и имя сервера в раскрывающемся списке.

   Нажмите кнопку **Готово** , чтобы завершить регистрацию. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Снимок экрана для завершения выбора":::

1. Выберите **настроить проверку** для хранилища, которое находится за брандмауэром и проверьте подключение. Соединение указывает на успешное выполнение. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Снимок экрана выбора T0 Настройка сканирования.":::

1. Настройте периодичность сканирования и нажмите кнопку **продолжить**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Снимок экрана выбора для запуска сканирования SQL.":::

1.  Сканирование завершается, а состояние обновляется в списке источников данных.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Снимок экрана: завершено сканирование сообщения":::
   
## <a name="next-steps"></a>Дальнейшие шаги

Затем можно настроить набор правил проверки [создать набор правил сканирования](create-a-scan-rule-set.md) для совместной группировки сканирований.
