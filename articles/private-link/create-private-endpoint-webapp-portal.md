---
title: Подключение к веб-приложению с помощью Azure Private Endpoint
description: Подключение к веб-приложению с помощью Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287821"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Подключитесь к веб-приложению с помощью частной конечных точек Azure (Предварительный просмотр)

Azure Private Endpoint является основным строительным блоком для Private Link в Azure. Это позволяет подключиться к веб-приложению в частном порядке.
В этом квикстарте вы узнаете, как развернуть web-приложение с частной конечной точкой и подключиться к этому веб-приложению из виртуальной машины.

Для получения дополнительной информации [см.][privatenedpointwebapp]

> [!Note]
>Предварительный просмотр доступен в Восточной США и Западе США 2 регионов для всех PremiumV2 Windows и Linux Web Apps. 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Виртуальная сеть и виртуальная машина

В этом разделе вы создадите виртуальную сеть и подсеть для размещения VM, который используется для доступа к вашему веб-приложению через частную конечную точку.

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

В этом разделе вы создадите виртуальную сеть и подсеть.

1. На верхней левой стороне экрана выберите **Создать ресурс** > **Сети** > **Виртуальная сеть** или поиск **виртуальной сети** в поле поиска.

1. В **создании виртуальной сети**введите или выберите эту информацию во вкладке Основы:

   > [!div class="mx-imgBorder"]
   > ![Создание виртуальной сети][1]

1. Нажмите **"Следующий: IP-адреса >"** и введите или выберите эту информацию:

   > [!div class="mx-imgBorder"]
   >![Настройка IP-адресов][2]

1. В разделе подсети щелкните **кнопку «Добавить поднет»** и введите следующую информацию и нажмите **кнопку «Добавить»**

   > [!div class="mx-imgBorder"]
   >![Добавить подсеть][3]

1. Нажмите **кнопку "Обзор и создать"**

1. После проверки нажмите **кнопку "Создать"**

### <a name="create-virtual-machine"></a>Создание виртуальной машины

1. На верхней левой стороне экрана на портале Azure выберите **Создать ресурс** > **Compute** > **Virtual machine**

1. В окне Создание виртуальной машины — Основы введите или выберите следующую информацию:

   > [!div class="mx-imgBorder"]
   >![Виртуальная машина основных][4]

1. Выберите **"Следующий: Диски"**

   Храните настройки по умолчанию.

1. Выберите **"Следующий: Сеть"**, выберите эту информацию:

   > [!div class="mx-imgBorder"]
   >![Сеть][5]

1. Нажмите **кнопку "Обзор и создание"**

1. При проверке пройдено сообщение, нажмите **кнопку "Создать"**

## <a name="create-your-web-app-and-private-endpoint"></a>Создайте веб-приложение и частную конечную точку

В этом разделе вы создадите частное Web-приложение с помощью частной конечной точки.

> [!Note]
>Функция Private Endpoint доступна только для Premium V2 SKU.

### <a name="web-app"></a>Веб-приложение

1. На верхней левой стороне экрана на портале Azure выберите **Создать ресурс** > **Web** > **Web App**

1. В Create Web App - Основы введите или выберите эту информацию:

   > [!div class="mx-imgBorder"]
   >![Веб-приложение базовое][6]

1. Выберите **"Обзор и создать"**

1. При проверке пройдено сообщение, нажмите **кнопку "Создать"**

### <a name="create-the-private-endpoint"></a>Создание частной конечной точки

1. В свойствах Web App выберите **Настройки** > **Сети** и нажмите на **кнопку "Настройте свои личные соединения конечных точек"**

   > [!div class="mx-imgBorder"]
   >![Сеть web App][7]

1. В мастере щелкните **кнопку "Я добавляй"**

   > [!div class="mx-imgBorder"]
   >![Веб-приложение Частная конечная точка][8]

1. Заполните информацию о подписке, VNet и Subnet и нажмите **кнопку "ОК"**

   > [!div class="mx-imgBorder"]
   >![Сеть веб-приложений][9]

1. Просмотрите создание частной конечной точки

   > [!div class="mx-imgBorder"]
   >![Обзор][10]
   >![Заключительный вид частной конечной точки][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

1. В панели поиска портала введите **myVm**
1. Выберите **кнопку «Подключение».** После выбора кнопки Connect, Подключите к виртуальной машине открывается, выберите **RDP**

   > [!div class="mx-imgBorder"]
   >![Кнопка RDP][12]

1. Azure создает файл удаленного протокола рабочего стола (.rdp) и загружает его на компьютер после нажатия на **файл Загрузки RDP**

   > [!div class="mx-imgBorder"]
   >![Скачать файл RDP][13]

1. Откройте файл downloaded.rdp.

- При появлении запроса выберите Подключиться.
- Введите имя пользователя и пароль, указанные при создании виртуальной машины.

> [!Note]
> Возможно, вам придется выбрать больше вариантов > использовать другую учетную запись, чтобы указать учетные данные, которые вы ввели при создании VM.

- Нажмите кнопку "ОК".

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите Да или Продолжить.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.

## <a name="access-web-app-privately-from-the-vm"></a>Доступ к веб-приложению в частном порядке от VM

В этом разделе вы подключитесь к Веб-приложению с помощью частной точки.

1. Получите приватный IP вашей частной конечной точки, в типе панели поиска **Приватная ссылка**, и выберите Приватную ссылку

   > [!div class="mx-imgBorder"]
   >![Приватный канал][14]

1. В центре частной ссылки выберите **частные конечные точки,** чтобы перечислить все ваши частные конечные точки

   > [!div class="mx-imgBorder"]
   >![Частный центр связи][15]

1. Выберите ссылку Private Endpoint на веб-приложение и подсеть

   > [!div class="mx-imgBorder"]
   >![Частные свойства конечных точек][16]

1. Копируйте частный IP вашей частной конечной точки и F-DN вашего веб-приложения, в нашем случае webappdemope.azurewebsites.net 10.10.2.4

1. В myVM убедитесь, что Web App не доступен через общедоступный IP. Откройте браузер и вставьте имя Web App, вы должны иметь 403 запрещенные страницы ошибки

   > [!div class="mx-imgBorder"]
   >![Запрещено][17]

> [!Important]
> Поскольку эта функция находится в предварительном просмотре, необходимо вручную управлять записью DNS.

1. Создайте вход хоста, откройте исследователь файлов и найдите файл хостов

   > [!div class="mx-imgBorder"]
   >![Файл хостов][18]

1. Добавьте запись с закрытым IP-адресом и общедоступным названием вашего веб-приложения, редактируя файл хостов с помощью блокнота

   > [!div class="mx-imgBorder"]
   >![Содержимое хостов][19]

1. Сохраните файл.

1. Откройте браузер и введите URL-адрес вашего веб-приложения

   > [!div class="mx-imgBorder"]
   >![Веб-сайт с PE][20]

1. Вы получаете доступ к своему веб-приложению через частную конечную точку

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы закончите использовать Private Endpoint, Web App и VM, удалите группу ресурсов и все ресурсы, которые она содержит:

1. Введите готовый rg в поле поиска в верхней части портала и выберите готовый rg из результатов поиска.
1. Выберите Удалить группу ресурсов.
1. Введите готовый rg для TYPE RESOURCE GROUP NAME и выберите Удалить.

## <a name="next-steps"></a>Дальнейшие действия

В этом квикстарте вы создали VM в виртуальной сети, веб-приложение и частную конечную точку. Вы подключились к VM из Интернета и надежно общались с Web App с помощью Private Link. Чтобы узнать больше о частной конечной точке, смотрите [Что такое Azure Private Endpoint][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
