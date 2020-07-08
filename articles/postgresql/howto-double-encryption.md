---
title: Двойное шифрование инфраструктуры — портал Azure — база данных Azure для PostgreSQL
description: Узнайте, как настроить и управлять двойным шифрованием инфраструктуры для базы данных Azure для PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2eb1c196e73644eeefa77d66562dc2a55d6f221a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034945"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Двойное шифрование инфраструктуры для базы данных Azure для PostgreSQL

Узнайте, как использовать двойное шифрование инфраструктуры для базы данных Azure для PostgreSQL и управлять им.

## <a name="prerequisites"></a>Предварительные условия

* Подписка Azure и права администратора для нее.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Создание базы данных Azure для сервера PostgreSQL с помощью двойного шифрования инфраструктуры — портал

Выполните следующие действия, чтобы создать сервер базы данных Azure для MySQL с двойным шифрованием инфраструктуры из портал Azure.

1. Выберите **Создать ресурс** (+) в левом верхнем углу страницы портала.

2. Выберите **databases**  >  **база данных Azure для PostgreSQL**. Можно также ввести PostgreSQL в поле поиска, чтобы найти службу. Включен параметр развертывания с **одиночным сервером** .

   ![Служба "База данных Azure для PostgreSQL" в меню](./media/quickstart-create-database-portal/1-create-database.png)

3. Укажите основные сведения о сервере. Выберите **Дополнительные параметры** и включите флажок **двойное шифрование инфраструктуры** , чтобы задать параметр.

    ![Параметры базы данных Azure для PostgreSQL](./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png)

4. Выберите **Проверка + создать** , чтобы подготавливает сервер.

    ![Сводка по базе данных Azure для PostgreSQL](./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png)

5. После создания сервера можно проверить двойное шифрование инфраструктуры, проверив состояние в колонке "сервер **шифрования данных** ".

    ![Проверка базы данных Azure для MySQL](./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Создание базы данных Azure для сервера PostgreSQL с помощью двойного шифрования инфраструктуры — CLI

Выполните следующие действия, чтобы создать сервер базы данных Azure для MySQL с двойным шифрованием инфраструктуры из CLI:

В этом примере создается группа ресурсов с именем `myresourcegroup` в `westus` расположении.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
В следующем примере создается сервер PostgreSQL 11 в западной части США под названием `mydemoserver` в группе ресурсов `myresourcegroup` с именем входа администратора сервера `myadmin` . Это сервер **4-го поколения** **общего назначения** с **двумя виртуальными ядрами**. При этом также будет включено двойное шифрование инфраструктуры для созданного сервера. Замените `<server_admin_password>` собственным значением.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о шифровании данных см. в статье шифрование данных в [базе данных Azure для PostgreSQL](concepts-Infrastructure-double-encryption.md).

