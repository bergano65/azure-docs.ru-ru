---
title: Хранилище таблиц Azure | Документация Майкрософт
description: Настройте управление интересами для Хранилища таблиц Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808469"
---
<a name="lead-management-instructions-for-azure-table"></a>Инструкции по управлению интересами для Хранилища таблиц Azure
============================================

В этой статье описывается, как настроить Хранилище таблиц Azure для хранения информации о потенциальных клиентах. Хранилище таблиц Azure позволяет сохранять и настраивать информацию о клиентах.

## <a name="to-configure-azure-table"></a>Настройка Хранилища таблиц Azure

1.  Если у вас нет учетной записи Azure, вы можете [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).

2.  Войдите на [портал Azure](https://portal.azure.com), после того как учетная запись Azure станет активна.
3.  Создайте учетную запись хранения на портале Azure. На следующем снимке экрана показано, как создать учетную запись хранения. Дополнительные сведения о ценах на службу хранилища см. на странице [Обзор цен на хранилище Azure](https://azure.microsoft.com/pricing/details/storage/).

    ![Шаги по созданию учетной записи хранения Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Скопируйте строку подключения учетной записи хранения для ключа и вставьте ее в поле **Строка подключения к учетной записи хранилища** на Портале Cloud Partner. Пример строки подключения: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Ключ к хранилищу Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Вы можете использовать [обозреватель службы хранилища Azure](http://azurestorageexplorer.codeplex.com/) или любое другое средство для просмотра данных в таблице хранилища. Данные также можно экспортировать в Хранилище таблиц Azure
.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Необязательно.)** Использование Функций Azure с Хранилищем таблиц

Если вы хотите настроить способ получения интересов, используйте [Функции Azure](https://azure.microsoft.com/services/functions/) с Хранилищем таблиц. Служба "Функции Azure" позволяет автоматизировать процесс формирования интересов.

Ниже показано, как создать функцию Azure, которая использует таймер. Каждые пять минут функция ищет новые записи в Хранилище таблиц Azure, а затем отправляет уведомление по электронной почте с помощью службы SendGrid.


1.  [Создайте](https://portal.azure.com/#create/SendGrid.SendGrid) бесплатную учетную запись службы SendGrid в своей подписке Azure.

    ![Создание SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Создание ключа API SendGrid 
    - Выберите **Управление**, чтобы перейти к пользовательскому интерфейсу SendGrid.
    - Выберите **Параметры**, **Ключи API**, а затем создайте ключ с полным доступом к отправке почты.
    - Сохраните ключ API.


    ![Ключ API SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Создайте](https://portal.azure.com/#create/Microsoft.FunctionApp) приложение-функцию Azure, у которого для параметра "План размещения" задано значение "План потребления".

    ![Создание приложения-функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Создайте определение функции.

    ![Создание определения функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Чтобы получить функцию для отправки обновления в определенное время, выберите **TimerTrigger-CSharp** в качестве параметра запуска.

     ![Параметр триггера времени для функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Замените код на вкладке "Разработка" приведенным ниже примером кода. Замените адреса электронной почты теми, которые нужно использовать для отправителя и получателя.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Фрагмент кода для функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Выберите **Интеграция** и **Входные данные**, чтобы определить подключение к Хранилищу таблиц Azure.

    ![Интеграция функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Введите имя таблицы и определите строку подключения, выбрав **Создать**.


    ![Подключение к таблице для функции Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Теперь определите выходные данные как SendGrid и оставьте значения по умолчанию.

    ![Выходные привязки SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Значения по умолчанию для выходных привязок SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Добавьте ключ API SendGrid в поле "Параметры приложения-функции", используя имя SendGridApiKey и значение, полученное из ключей API в пользовательском интерфейсе SendGrid.

    ![Управление SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![Управление ключами SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Когда вы завершите настройку функции, код в разделе "Интеграция" должен выглядеть следующим образом:

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Наконец, перейдите в пользовательский интерфейс разработки функции и выберите **Запуск**, чтобы запустить таймер. Теперь вы будете получать уведомление каждый раз, когда будет поступать новый интерес.
