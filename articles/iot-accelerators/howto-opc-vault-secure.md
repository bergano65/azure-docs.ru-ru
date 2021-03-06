---
title: Защита обмена данными между устройствами OPC UA и хранилищем OPC в Azure | Документация Майкрософт
description: Как регистрировать приложения OPC UA и выдавать подписанные сертификаты приложений для устройств OPC UA с помощью хранилища OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454193"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Использование службы управления сертификатами хранилища OPC

В этой статье описывается регистрация приложений и выдача подписанных сертификатов приложений для устройств OPC UA.

## <a name="prerequisites"></a>Технические условия

### <a name="deploy-the-certificate-management-service"></a>Развертывание службы управления сертификатами

Сначала разверните службу в облаке Azure. Дополнительные сведения см. [в разделе Развертывание службы управления сертификатами хранилища OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Создание сертификата ЦС издателя

Если вы еще не сделали этого, создайте сертификат ЦС поставщика. Дополнительные сведения см. [в статье Создание сертификата издателя для хранилища OPC и управление им](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Защита приложений OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Шаг 1. Регистрация приложения OPC UA 

> [!IMPORTANT]
> Для регистрации приложения требуется роль модуля записи.

1. Откройте службу сертификатов по адресу `https://myResourceGroup-app.azurewebsites.net`и выполните вход.
2. Перейдите к разделу **Регистрация новых**. Для регистрации приложения пользователю необходимо иметь по крайней мере назначенную роль модуля записи.
2. Форма ввода соответствует соглашениям об именовании в OPC UA. Например, на следующем снимке экрана показаны параметры примера [эталонного сервера OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) в .NET Standardном стеке OPC UA:

   ![Снимок экрана с регистрацией эталонного сервера UA](media/howto-opc-vault-secure/reference-server-registration.png "Регистрация эталонного сервера UA")

5. Выберите **зарегистрировать** , чтобы зарегистрировать приложение в базе данных приложения службы сертификатов. Рабочий процесс непосредственно направляет пользователя к следующему шагу, чтобы запросить подписанный сертификат для приложения.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Шаг 2. Защита приложения с помощью сертификата подписанного приложения ЦС

Защитите приложение OPC UA, выполнив подписанный сертификат на основе запроса подписи сертификата (CSR). Кроме того, можно запросить новую пару ключей, которая включает новый закрытый ключ в формате PFX или PEM. Сведения о том, какой метод поддерживается для вашего приложения, см. в документации к устройству OPC UA. Как правило, рекомендуется использовать метод CSR, поскольку ему не требуется передавать закрытый ключ по сети.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Запросить новый сертификат с помощью новой пары ключей

1. Перейдите в раздел **приложения**.
3. Выберите **новый запрос** для указанного приложения.

   ![Снимок экрана запроса нового сертификата](media/howto-opc-vault-secure/request-new-certificate.png "Запросить новый сертификат")

3. Выберите **запросить новую пару ключей и сертификат** , чтобы запросить закрытый ключ и новый подписанный сертификат с открытым ключом для вашего приложения.

   ![Снимок экрана создания новой пары ключей и сертификата](media/howto-opc-vault-secure/generate-new-key-pair.png "Создать новую пару ключей")

4. Заполните форму, указав тему и доменные имена. Для параметра закрытый ключ выберите PEM или PFX с паролем. Выберите **создать новую пару ключей** , чтобы создать запрос на сертификат.

   ![Снимок экрана: Просмотр сведений о запросе сертификата](media/howto-opc-vault-secure/approve-reject.png "Утвердить сертификат")

5. Для утверждения требуется пользователь с ролью утверждающего и с разрешениями на подписывание в Azure Key Vault. В обычном рабочем процессе роли утверждающего и запросившего должны быть назначены разным пользователям. Выберите **утвердить** или **отклонить** , чтобы запустить или отменить фактическое создание пары ключей и операции подписывания. Новая пара ключей создается и сохраняется безопасно в Azure Key Vault, пока не будет скачана инициатором запроса сертификата. Полученный сертификат с открытым ключом подписывается центром сертификации. Для завершения этих операций может потребоваться несколько секунд.

   ![Снимок экрана: Просмотр сведений о запросе сертификата с сообщением об утверждении в нижней части](media/howto-opc-vault-secure/view-key-pair.png "Просмотреть пару ключей")

7. Полученный закрытый ключ (PFX или PEM) и сертификат (DER) можно скачать отсюда в формате, выбранном как Загрузка двоичных файлов. Версия с кодировкой base64 также доступна, например, для копирования и вставки сертификата в командную строку или в текстовую запись. 
8. После скачивания и безопасного сохранения закрытого ключа можно выбрать параметр **Удалить закрытый ключ**. Сертификат с открытым ключом остается доступным для будущего использования.
9. Из-за использования сертификата, подписанного центром сертификации, также следует скачать сертификат ЦС и список отзыва сертификатов (CRL).

Теперь он зависит от устройства OPC UA, как применить новую пару ключей. Как правило, сертификаты ЦС и CRL копируются в папку `trusted`, а открытый и закрытый ключи сертификата приложения применяются к папке `own` в хранилище сертификатов. Некоторые устройства могут уже поддерживать принудительную отправку сервером обновлений сертификатов. См. документацию по устройству OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Запрос нового сертификата с помощью CSR 

1. Перейдите в раздел **приложения**.
3. Выберите **новый запрос** для указанного приложения.

   ![Снимок экрана запроса нового сертификата](media/howto-opc-vault-secure/request-new-certificate.png "Запросить новый сертификат")

3. Выберите **запросить новый сертификат с запросом на подписывание** , чтобы запросить новый подписанный сертификат для приложения.

   ![Снимок экрана создания нового сертификата](media/howto-opc-vault-secure/generate-new-certificate.png "Создать новый сертификат")

4. Отправьте CSR, выбрав локальный файл или вставляя CSR в кодировке Base64 в форме. Выберите **создать новый сертификат**.

   ![Снимок экрана: Просмотр сведений о запросе сертификата](media/howto-opc-vault-secure/approve-reject-csr.png "Утверждение CSR")

5. Для утверждения требуется пользователь с ролью утверждающего и с разрешениями на подписывание в Azure Key Vault. Выберите **утвердить** или **отклонить** , чтобы запустить или отменить фактическую операцию подписывания. Полученный сертификат с открытым ключом подписывается центром сертификации. Для завершения этой операции может потребоваться несколько секунд.

   ![Снимок экрана: Просмотр сведений о запросе сертификата с сообщением об утверждении в нижней части](media/howto-opc-vault-secure/view-cert-csr.png "Просмотр сертификата")

6. Полученный сертификат (DER) можно скачать отсюда в виде двоичного файла. Версия с кодировкой base64 также доступна, например, для копирования и вставки сертификата в командную строку или в текстовую запись. 
10. После скачивания и безопасного сохранения сертификата можно выбрать **удалить сертификат**.
11. Из-за использования сертификата, подписанного центром сертификации, также следует скачать сертификат ЦС и список отзыва сертификатов.

Теперь он зависит от устройства OPC UA, как применить новый сертификат. Как правило, сертификаты ЦС и CRL копируются в папку `trusted`, а сертификат приложения применяется к папке `own` в хранилище сертификатов. Некоторые устройства могут уже поддерживать принудительную отправку сервером обновлений сертификатов. См. документацию по устройству OPC UA.

### <a name="step-3-device-secured"></a>Шаг 3. Защита устройства

Теперь устройство OPC UA готово к взаимодействии с другими устройствами OPC UA, защищенными сертификатами, подписанными ЦС, без дальнейшей настройки.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как защитить устройства OPC UA, вы можете:

> [!div class="nextstepaction"]
> [Запуск службы безопасного управления сертификатами](howto-opc-vault-secure-ca.md)
