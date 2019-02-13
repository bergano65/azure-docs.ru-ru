---
title: Преобразование XML с помощью карт XSLT — Azure Logic Apps | Документация Майкрософт
description: Добавление карт XSLT для преобразования XML в Azure Logic Apps с помощью Пакета интеграции Enterprise
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: da5b099a5574d34c3676819c930f3e89610cf4ad
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767441"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Преобразование XML с помощью карт в Azure Logic Apps с использованием Пакета интеграции Enterprise

Для передачи XML-данных между форматами для сценариев корпоративной интеграции в Azure Logic Apps ваше приложение логики может использовать карты, а точнее карты XSLT. Карта — это XML-документ, описывающий преобразование данных из XML-документа в другой формат. 

Предположим, вы регулярно получаете заказы или счета от клиентов "бизнес — бизнес" (B2B), которые используют формат даты ГГГММДД. Однако ваша организация использует дату в формате MMДДГГГ. Вы можете определить и использовать карту, чтобы преобразовать формат даты ГГГММДД в ММДДГГГ перед сохранением сведений о заказе или счете в базе данных деловой активности клиента.

Сведения об ограничениях, связанных с учетными записями интеграции и артефактами, такими как карты, см. в статье [Ограничения и сведения о конфигурации для Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрироваться для получения бесплатной учетной записи Azure</a>.

* [Учетная запись интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), в которой хранятся ваши карты и другие артефакты для корпоративной интеграции и решений "бизнес — бизнес" (B2B).

* Если карта ссылается на внешнюю сборку, необходимо передать *сборку и карту* в учетную запись интеграции. Сначала нужно *передать сборку*, а затем — карту со ссылкой на сборку.

  Если размер сборки до 2 МБ, вы можете добавить ее в учетную запись интеграции *непосредственно* с портала Azure. Однако, если размер сборки или карты превышает 2 МБ, но не превышает [ограничение размера для сборок или карт](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), доступны следующие варианты:

  * Для сборок необходим контейнер больших двоичных объектов Azure, в который можно отправить сборку, и расположение этого контейнера. Так вы сможете указать это расположение позже — при добавлении сборки в учетную запись интеграции. 
  Чтобы сделать это, вам понадобятся следующие элементы:

    | Элемент | ОПИСАНИЕ |
    |------|-------------|
    | [Учетная запись хранения Azure](../storage/common/storage-account-overview.md) | В этой учетной записи создайте контейнер больших двоичных объектов Azure для сборки. Узнайте, как [создать учетную запись хранения](../storage/common/storage-quickstart-create-account.md). |
    | Контейнер BLOB-объектов | В этот контейнер можно передать сборку. Расположение этого контейнера также требуется при добавлении сборки в учетную запись интеграции. Узнайте, как [создать контейнер больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer;](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Это средство упрощает управление учетными записями хранения и контейнерами больших двоичных объектов. Чтобы использовать Обозреватель службы хранилища Azure, [скачайте и установите его](https://www.storageexplorer.com/). Затем подключите Обозреватель службы хранилища к учетной записи хранения, следуя шагам из статьи [Начало работы с Обозревателем службы хранилища](../vs-azure-tools-storage-manage-with-storage-explorer.md). Дополнительные сведения см. в статье [ с кратким руководством по созданию большого двоичного объекта в хранилище объектов с помощью Обозревателя службы хранилища Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Можно также найти и выбрать свою учетную запись хранения на портале Azure. В меню учетной записи хранения выберите **Обозреватель службы хранилища**. |
    |||

  * Сейчас можно добавить карты большего размера с помощью [REST API Azure Logic Apps для работы с картами](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Приложение логики при создании и добавлении карт не требуется. Чтобы использовать карту, приложению логики требуется связь с учетной записью интеграции, в которой хранится эта карта. Узнайте, как [связать приложения логики с учетными записями интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Если у вас еще нет приложения логики, узнайте, как [его создать](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-referenced-assemblies"></a>Добавление сборок, на которые делаются ссылки

1. Войдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> с помощью учетных данных учетной записи Azure.

1. Чтобы найти и открыть учетную запись интеграции, в главном меню Azure выберите **Все службы**. 
   В поле поиска введите "учетная запись интеграции". 
   Щелкните **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Выберите учетную запись интеграции, в которую нужно добавить сборку, например:

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. На странице **Обзор** учетной записи интеграции в разделе **Компоненты** выберите плитку **Сборки**.

   ![Щелкните "Сборки"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Когда откроется страница **Сборки**, нажмите кнопку **Добавить**.

   ![Кнопка "Добавить"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

В зависимости от размера файла сборки выполните шаги по отправке сборки размером [до 2 МБ](#smaller-assembly) или [более 2 МБ, но до 8 МБ](#larger-assembly).
Дополнительные сведения об ограничениях на количество сборок в учетных записях интеграции см. в статье [Ограничения и сведения о конфигурации для Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Добавление сборок размером до 2 МБ

1. В разделе **Добавить сборку** введите имя сборки. Оставьте выбранным параметр **Мелкий файл**. Выберите значок папки рядом с полем **Сборка**. Найдите и выберите сборку, которую вы отправляете, например:

   ![Отправка сборки меньшего размера](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Имя файла сборки автоматически отображается в свойстве **Имя сборки** после выбора сборки.

1. Когда вы будете готовы, выберите **ОК**.

   Когда файл сборки будет отправлен, сборка появится в списке **Сборки**.

   ![Список отправленных сборок](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   На странице **Обзор** учетной записи интеграции в разделе **Компоненты** на плитке **Сборки** теперь отображается число отправленных сборок, например:

   ![Отправленные сборки](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Добавление сборок размером более 2 МБ

Чтобы добавить объемные сборки, отправьте свою сборку в контейнер больших двоичных объектов Azure в учетной записи хранения Azure. Ваши действия по добавлению сборок отличаются в зависимости от наличия у контейнера больших двоичных объектов общего доступа на чтение. Поэтому сначала проверьте, имеет ли контейнер больших двоичных объектов общий доступ на чтение, сделав следующее: [Настройка уровня общего доступа для контейнера больших двоичных объектов](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Проверка уровня доступа к контейнеру

1. Откройте обозреватель службы хранилища Azure. В окне Обозревателя службы хранилища разверните свою подписку Azure, если она еще не развернута.

1. Разверните узлы **Учетные записи хранения** > {*ваша учетная запись хранения*} > **Контейнеры больших двоичных объектов**. Выберите свой контейнер больших двоичных объектов.

1. В контекстном меню контейнера больших двоичных объектов выберите **Set Public Access Level** (Настроить уровень общего доступа).

   * Если контейнер больших двоичных объектов имеет по крайней мере общий доступ, выберите **Отменить** и выполните указанные ниже на странице шаги: [Отправка в контейнеры с общим доступом](#public-access-assemblies)

     ![Открытый доступ](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Если в контейнере больших двоичных объектов нет общего доступа, щелкните **Отменить** и следуйте указанным шагам на этой странице: [Отправка в контейнеры без общего доступа](#no-public-access-assemblies)

     ![Без открытого доступа](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Отправка в контейнеры с общим доступом

1. Отправьте сборку в учетную запись хранения. 
   В окне справа выберите **Отправить**.

1. После завершения операции выберите отправленную сборку. На панели инструментов выберите **Копировать URL-адрес**, чтобы скопировать URL-адрес сборки.

1. Вернитесь на портал Azure, где открыта панель **Добавление сборки**. 
   Введите имя сборки. 
   Выберите **Large file (larger than 2 MB)** (Большой файл (более 2 МБ)).

   Теперь отображается поле **URI содержимого**, а не **Сборка**.

1. В поле **URI содержимого** вставьте URL-адрес своей сборки. 
   Завершите добавление своей сборки.

После завершения отправки сборки схема появится в списке **Сборки**.
На странице **Обзор** учетной записи интеграции в разделе **Компоненты** на плитке **Сборки** теперь отображается число отправленных сборок.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Отправка в контейнеры без общего доступа

1. Отправьте сборку в учетную запись хранения. 
   В окне справа выберите **Отправить**.

1. После отправки создайте подписанный URL-адрес сборки. 
   В контекстном меню сборки щелкните **Get Shared Access Signature** (Получить подписанный URL-адрес).

1. В области **Подписанный URL-адрес** выберите **Generate container-level shared access signature URI** (Создание URI подписанного URL-адреса уровня контейнера) > **Создать**. 
   После создания подписанного URL-адреса возле поля **URL-адрес** щелкните **Копировать**.

1. Вернитесь на портал Azure, где открыта панель **Добавление сборки**. 
   Введите имя сборки. 
   Выберите **Large file (larger than 2 MB)** (Большой файл (более 2 МБ)).

   Теперь отображается поле **URI содержимого**, а не **Сборка**.

1. В поле **URI содержимого** вставьте созданный ранее URI подписанного URL-адреса. Завершите добавление своей сборки.

После завершения отправки сборки она появится в списке **Схемы**. На странице **Обзор** учетной записи интеграции в разделе **Компоненты** на плитке **Сборки** теперь отображается число отправленных сборок.

## <a name="create-maps"></a>Создание карт

Чтобы создать документ XSLT для использования в качестве карты, создайте в Visual Studio 2015 проект интеграции BizTalk с помощью [Пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md). В этом проекте можно создать файл карты интеграции, чтобы визуально сопоставить элементы между двумя файлами схем XML. После создания этого проекта вы получите документ XSLT.
Дополнительные сведения см. в статье [Ограничения и сведения о конфигурации для Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Добавление карт

После отправки всех сборок, на которые ссылается карта, можно отправить карту.

1. Если вы еще не выполнили вход, войдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> с использованием учетных данных учетной записи Azure. 

1. Если учетная запись интеграции еще не открыта, в главном меню Azure щелкните **Все службы**. 
   В поле поиска введите "учетная запись интеграции". 
   Щелкните **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Выберите учетную запись интеграции, в которую нужно добавить карту, например:

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. На странице **Обзор** учетной записи интеграции в разделе **Компоненты** выберите плитку **Карты**.

   ![Выбор элемента "Карты"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Когда откроется страница **Карты**, нажмите кнопку **Добавить**.

   ![Кнопка "Добавить"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Добавление карт размером до 2 МБ

1. В разделе **Добавление карты** введите имя карты. 

1. В разделе **Тип карты** выберите тип, например: **Liquid**, **XSLT**, **XSLT 2.0** или **XSLT 3.0**.

1. Оставьте выбранным параметр **Мелкий файл**. Выберите значок папки рядом с полем **Карта**. Найдите и выберите карту, которую вы отправляете, например:

   ![Отправка карты](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Если оставить свойство **Имя** пустым, имя файла карты автоматически появится в этом свойстве после выбора файла карты. 
   Однако вы можете использовать любое уникальное имя.

1. Когда вы будете готовы, выберите **ОК**. 
   Когда файл карты будет отправлен, карта появится в списке **Карты**.

   ![Список отправленных карт](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   На странице **Обзор** учетной записи интеграции в разделе **Компоненты** на плитке **Карты** теперь отображается число отправленных карт, например:

   ![Отправленные карты](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Добавление карт размером более 2 МБ

Сейчас для добавления карт большого размера используйте [REST API Azure Logic Apps для работы с картами](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Изменение карт

Чтобы обновить имеющуюся карту, нужно отправить новый файл карты с необходимыми изменениями. Однако сначала вы можете скачать имеющуюся карту для редактирования.

1. На <a href="https://portal.azure.com" target="_blank">портале Azure</a> найдите и выберите свою учетную запись интеграции, если она еще не открыта.

1. В главном меню Azure выберите **Все службы**. В поле поиска введите "учетная запись интеграции". Щелкните **Учетные записи интеграции**.

1. Выберите учетную запись интеграции, в которой необходимо обновить карту.

1. На странице **Обзор** учетной записи интеграции в разделе **Компоненты** выберите плитку **Карты**.

1. Когда откроется страница **Карты**, выберите свою карту. 
   Чтобы скачать и изменить карту, щелкните **Скачать** и сохраните схему.

1. Когда все будет готово для отправки обновленной карты, на странице **Карты** выберите карту, которую необходимо обновить, и щелкните **Обновить**.

1. Найдите и выберите обновленную карту, которую требуется отправить. 
   После отправки файла карты обновленная карта появится в списке **Карты**.

## <a name="delete-maps"></a>Удаление карт

1. На <a href="https://portal.azure.com" target="_blank">портале Azure</a> найдите и выберите свою учетную запись интеграции, если она еще не открыта.

1. В главном меню Azure выберите **Все службы**. 
   В поле поиска введите "учетная запись интеграции". 
   Щелкните **Учетные записи интеграции**.

1. Выберите учетную запись интеграции, в которой необходимо удалить карту.

1. На странице **Обзор** учетной записи интеграции в разделе **Компоненты** выберите плитку **Карты**.

1. После открытия страницы **Карты** щелкните свою карту, а затем нажмите кнопку **Удалить**.

1. Чтобы подтвердить удаление карты, щелкните **Да**.

## <a name="next-steps"></a>Дополнительная информация

* [Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Дополнительные сведения о схемах](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Дополнительные сведения о преобразованиях](../logic-apps/logic-apps-enterprise-integration-transform.md)