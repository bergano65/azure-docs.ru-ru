---
title: Подключение к Azure Analysis Services с помощью Excel | Документы Майкрософт
description: Сведения о подключении к серверу Azure Analysis Services с помощью Excel.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1ba6864d0bab460b270f6f27acced57d7943a17a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295334"
---
# <a name="connect-with-excel"></a>Подключение с помощью Excel

После создания сервера и развертывания на нем табличной модели клиенты смогут подключиться к нему для работы с данными. 

## <a name="before-you-begin"></a>Перед началом работы

Учетная запись, с помощью которой вы вошли в систему, должна принадлежать к роли шаблона базы данных по крайней мере с правами на чтение. Дополнительные сведения см. в руководстве по [аутентификации и настройке пользовательских разрешений](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Подключение в Excel

Подключиться к серверу из Excel можно с помощью функции "Получить данные" в Excel 2016 и более поздних версий. Подключение с помощью мастера импорта таблиц в Power Pivot не поддерживается. 

1. В Excel на ленте **Данные** щелкните **Получить внешние данные** > **Из других источников** > **Из служб Analysis Services**.

2. В мастере подключения к данным введите имя сервера, включая протокол и универсальный код ресурса (URI), в поле **Имя сервера**. Например, asazure://westcentralus.asazure.windows.net/advworks. Затем для параметра **Учетные данные** выберите значение **Использовать следующие имя пользователя и пароль**, введите имя пользователя, например nancy@adventureworks.com, и пароль для него.

    > [!IMPORTANT]
    > Если вы входите в систему с учетной записью Майкрософт, Live ID, Yahoo, Gmail, и т. д. или требуется войти с помощью многофакторной проверки подлинности, оставьте поле пароля пустым. Пароль запрашивается после нажатия кнопки "Далее". 

    ![Подключение из Excel: вход](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. В разделе **Выбор базы данных и таблицы** выберите нужную базу данных, а также модель или перспективу, и нажмите кнопку **Готово**.
   
    ![Подключение из Excel: выбор модели](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>См. также

[Клиентские библиотеки](analysis-services-data-providers.md)   
[Управление службами Analysis Services](analysis-services-manage.md)     


