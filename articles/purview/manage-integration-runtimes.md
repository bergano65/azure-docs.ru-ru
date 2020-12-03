---
title: Создание сред выполнения интеграции и управление ими
description: В этой статье описаны действия по созданию и управлению средой выполнения интеграции в Azure зрения.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553656"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Создание локальной среды выполнения интеграции и управление ей

В этой статье описывается создание локальной среды выполнения интеграции (шир) и управление ей для помощи в поиске источников данных.

## <a name="create-a-self-hosted-integration-runtime"></a>Создание локальной среды выполнения интеграции

1. На домашней странице зрения Studio в области навигации слева выберите **центр управления** .

2. В разделе **источники и сканирование** в левой области выберите **Integration Runtimes (среды выполнения интеграции**), а затем щелкните **+ создать**.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Щелкните IR.":::

3. На странице **Настройка среды выполнения интеграции** выберите локальное **Размещение** , чтобы создать Self-Hosted IR, а затем нажмите кнопку **продолжить**.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Создайте новый шир.":::

4. Введите имя для своего типа IR и нажмите кнопку Создать.

5. На странице **параметры Integration Runtime** выполните действия, описанные в разделе **Настройка вручную** . Необходимо загрузить среду выполнения интеграции с сайта загрузки на ВИРТУАЛЬную машину или компьютер, где планируется ее запустить.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="получить ключ":::

    a. Скопируйте и вставьте ключ проверки подлинности.
        
    b. Скачайте локальную среду выполнения интеграции из [фабрики данных Azure Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) на локальном компьютере Windows. Запустите установщик.
        
    c. На странице **регистрация Integration Runtime (Self-Hosted)** вставьте один из двух сохраненных ранее ключей и нажмите кнопку **зарегистрировать**.

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="ключ ввода.":::

    d. На странице **Новый узел среды выполнения интеграции (с локальным размещением)** нажмите кнопку **Finish** (Завершить)

6. После успешной регистрации локальной среды выполнения интеграции вы увидите следующее окно:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Регистрация успешно выполнена.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Управление локальной средой выполнения интеграции

Локальную среду выполнения интеграции можно изменить, перейдя **в** **центр управления**, выбрав IR, а затем выбрав пункт изменить. Теперь можно обновить описание, скопировать ключ или повторно создать новые ключи.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="изменить IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="изменение сведений о IR.":::

Локальную среду выполнения интеграции можно удалить, перейдя **в центр** управления, выбрав ее, а затем щелкнув **Удалить**. После удаления инфракрасной связи все текущие проверки, использующие ее, завершатся ошибкой.

## <a name="next-steps"></a>Дальнейшие шаги

* [Обнаружение удаленных ресурсов при сканировании](concept-detect-deleted-assets.md)
