---
title: Двойное шифрование инфраструктуры — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить и управлять двойным шифрованием инфраструктуры для базы данных Azure для MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: d3076f2591718931bdab4dba9510d25fe07b2d02
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118766"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Двойное шифрование инфраструктуры для базы данных Azure для MySQL

Узнайте, как использовать двойное шифрование инфраструктуры для базы данных Azure для MySQL и управлять им.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure и права администратора для нее.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Создание базы данных Azure для сервера MySQL с помощью двойного шифрования инфраструктуры — портал

Выполните следующие действия, чтобы создать сервер базы данных Azure для MySQL с двойным шифрованием инфраструктуры из портал Azure.

1. Выберите **Создать ресурс** (+) в левом верхнем углу страницы портала.

2. Выберите **Базы данных** > **База данных Azure для MySQL**. Чтобы найти службу, вы также можете ввести в поле поиска **MySQL**.

   ![Пункт "База данных Azure для MySQL"](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Укажите основные сведения о сервере. Выберите **Дополнительные параметры** и включите флажок **двойное шифрование инфраструктуры** , чтобы задать параметр.

    ![Параметры базы данных Azure для MySQL](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. Выберите **Проверка + создать** , чтобы подготавливает сервер.

    ![Сводка по базе данных Azure для MySQL](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. После создания сервера можно проверить двойное шифрование инфраструктуры, проверив состояние в колонке "сервер **шифрования данных** ".

    ![Проверка базы данных Azure для MySQL](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Создание базы данных Azure для сервера MySQL с помощью двойного шифрования инфраструктуры — CLI

Выполните следующие действия, чтобы создать сервер базы данных Azure для MySQL с двойным шифрованием инфраструктуры из CLI:

В этом примере создается группа ресурсов с именем `myresourcegroup` в `westus` расположении.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
В примере ниже показано создание сервера MySQL 5.7 с именем `mydemoserver` в группе ресурсов `myresourcegroup` с именем пользователя администратора сервера `myadmin`. Это сервер **4-го поколения** **общего назначения** с **двумя виртуальными ядрами**. При этом также будет включено двойное шифрование инфраструктуры для созданного сервера. Замените `<server_admin_password>` собственным значением.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [двойное шифрование инфраструктуры данных в базе данных Azure для MySQL](concepts-Infrastructure-double-encryption.md).
