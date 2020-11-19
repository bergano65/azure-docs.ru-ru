---
title: Обновление основной версии — портал Azure — база данных Azure для MySQL — один сервер
description: В этой статье описывается, как можно обновить основную версию базы данных Azure для MySQL — одиночный сервер с помощью портал Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920650"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Обновление основной версии в базе данных Azure для MySQL на одном сервере с помощью портал Azure

> [!IMPORTANT]
> Обновление основной версии для одного сервера базы данных Azure для MySQL доступно в общедоступной предварительной версии.

В этой статье описывается, как можно обновить основную версию MySQL на месте в базе данных Azure для MySQL Single Server.

Эта функция позволит клиентам выполнять обновление на месте серверов MySQL 5,6 до MySQL 5,7 с помощью кнопки без перемещения данных или изменения строки подключения приложения.

> [!Note]
> * Обновление основной версии доступно только для обновления основной версии с MySQL 5,6 до MySQL 5,7<br>
> * Обновление основной версии на сервере реплики пока не поддерживается.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [Один сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Выполнение обновления основной версии с MySQL 5,6 до MySQL 5,7

Выполните следующие действия, чтобы выполнить обновление основной версии для базы данных Azure сервера MySQL 5,6.

> [!IMPORTANT]
> Рекомендуется сначала выполнить обновление на восстановленной копии сервера, а не напрямую обновлять рабочую среду. См. раздел [как выполнить восстановление до точки во времени](howto-restore-server-portal.md#point-in-time-restore).

1. В [портал Azure](https://portal.azure.com/)выберите существующий сервер базы данных Azure для MySQL 5,6.

2. На странице **Обзор** нажмите кнопку **Обновить** на панели инструментов.

3. В разделе **Обновление** нажмите кнопку **ОК** , чтобы обновить сервер базы данных Azure для MySQL 5,6 на сервер 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Обзор базы данных Azure для MySQL — обновление":::

4. В уведомлении будет подтверждено, что обновление прошло успешно.

## <a name="next-steps"></a>Следующие шаги

Сведения о [политике управления версиями базы данных Azure для MySQL](concepts-version-policy.md).