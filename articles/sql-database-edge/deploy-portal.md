---
title: Развертывание предварительного просмотра edge базы данных с помощью портала Azure (англ.) Документы Майкрософт
description: Узнайте, как развернуть край базы данных Azure S'L с помощью портала Azure
keywords: развертывание края базы данных sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246727"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Развертывание Edge базы данных Azure S'L

Azure S'L Database Edge Preview — это реляционная база данных, оптимизированная для развертывания IoT и Azure IoT Edge. Он предоставляет возможности для создания высокопроизводительного слоя для хранения и обработки данных для приложений и решений IoT. Этот быстрый запуск показывает, как начать работу с созданием модуля Azure S'L Database Edge через Azure IoT Edge с помощью портала Azure.

## <a name="before-you-begin"></a>Перед началом

* Если у вас нет подписки На Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Войдите на [портал Azure](https://portal.azure.com/).
* Отправьте запрос [здесь,](https://azure.microsoft.com/services/sql-database-edge/#contact)чтобы иметь подписку включена для развертывания S'L Базисного края.
* Создайте [концентратор Azure IoT.](../iot-hub/iot-hub-create-through-portal.md)
* Зарегистрируйте [устройство IoT Edge с портала Azure.](../iot-edge/how-to-register-device-portal.md)
* Подготовьте устройство IoT Edge для [развертывания модуля IoT Edge с портала Azure.](../iot-edge/how-to-deploy-modules-portal.md)

> [!NOTE]
> Чтобы развернуть Azure Linux VM в качестве устройства IoT Edge, смотрите это [руководство по быстрому запуску.](../iot-edge/quickstart-linux.md)

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Развертывание edge базы данных S'L с azure Marketplace

Azure Marketplace — это интернет-магазин приложений и служб, где вы можете просматривать разнообразные корпоративные приложения и решения, сертифицированные и оптимизированные для работы в Azure, включая [модули IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Край базы данных Azure S'L может быть развернут на устройстве края через рынок.

1. Найдите модуль Azure S'L Database Edge на рынке Azure.<br><br>

   ![Край базы данных S'L на MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Выберите программный план, который наилучшим образом соответствует вашим требованиям и нажмите **Создать**. <br><br>

   ![Выберите правильный программный план](media/deploy-portal/pick-correct-plan.png)

3. На целевой странице Модуля IoT Edge Укажите следующие детали, а затем нажмите **«Создать»**

   |**Поле**  |**Описание**  |
   |---------|---------|
   |Подписка  |  Подписка Azure, в рамках которой был создан концентратор IoT |
   |Центр Интернета вещей   |  Название концентратора IoT, где зарегистрировано устройство IoT Edge, а затем выберите опцию «Развертывание на устройство»|
   |Имя устройства IoT Edge  |  Название устройства IoT Edge, на котором будет развернута край базы данных S'L |

4. На странице **Set Modules** перейдите в раздел о модулях развертывания и нажмите **Нанастройку** по модулю базы данных S'L. 

5. На панели **пользовательских модулей IoT Edge** укажите желаемые значения для переменных среды и/или настройте параметры создания и желаемые свойства для модуля. Для получения полного списка поддерживаемых переменных среды ссылаются [переменные среды контейнерного контейнера S'L Server.](/sql/linux/sql-server-linux-configure-environment-variables/)

   |**Параметр**  |**Описание**|
   |---------|---------|
   | name | Название модуля. |
   |SA_PASSWORD  | Укажите надежный пароль для учетной записи админ-аккаунта S'L Database Edge. |
   |MSSQL_LCID   | Задайте идентификатор языка для SQL Server. Например, 1036 - это французский. |
   |MSSQL_COLLATION | Задает параметры сортировки по умолчанию для SQL Server. Эта настройка перекрывает отображение идентификатора языка по умолчанию (LCID) для сопоставления. |

   > [!NOTE]
   > Пожалуйста, не изменяйте и не обновляйте **uri изображения** или **настройки ACCEPT_EULA** на модуле.

6. На панели **пользовательских модулей IoT Edge** обновите параметры создания контейнера для **порта Host.** Если вам необходимо развернуть несколько модулей S'L DB Edge, пожалуйста, упомянитесь, чтобы обновить опцию крепления для создания нового исходного & целевой пары для постоянного объема. Для получения дополнительной информации о креплениях и объеме, перекайтесь [Объемы использования](https://docs.docker.com/storage/volumes/) в документации докера. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. На панели **пользовательских модулей IoT Edge** обновите *желаемые свойства двухместных модулей Set,* чтобы включить расположение пакета S'L и информацию о работе по работе с потоковой аналитикой. Эти два поля являются необязательными и должны использоваться, если вы хотите развернуть модуль S'L Database Edge с базой данных и заданиями потоковой передачи.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. На панели **пользовательских модулей IoT Edge** установите *политику перезагрузки* всегда и *желаемого статуса* для запуска.
9. На панели **пользовательских модулей IoT Edge** нажмите **Кнопка Сохранить**.
10. На странице **модулей Set** нажмите **далее**.
11. На **сайте Specify Route (необязательно)** **страницы Set Modules** укажите маршруты для модуля модуля или модуля для связи IoT Edge Hub [см. Модули развертывания и налаживания маршрутов в IoT Edge.](../iot-edge/module-composition.md)
12. Нажмите кнопку **Далее**.
13. Щелкните элемент **Отправить**.

В этом быстром запуске вы развернули модуль Edge Базы данных на устройстве IoT Edge.

## <a name="next-steps"></a>Next Steps

- [Машинное обучение и искусственный интеллект с ONNX в Edge базы данных S'L](onnx-overview.md).
- Создание решения для окончания IoT с помощью Edge базы данных с помощью IoT Edge.
