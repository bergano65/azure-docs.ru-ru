---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260860"
---
## <a name="webapi-project"></a>Проект веб-интерфейса API

1. В Visual Studio откройте проект **AppBackend**, созданный в руководстве об **уведомлении пользователей**.
2. Замените в файле Notifications.cs целый класс **Уведомления** на следующий код. Убедитесь, что заменили заполнители на строку подключения (с полным доступом) для центра уведомлений и имени центра. Эти значения можно получить на [портале Azure](https://portal.azure.com). В этом модуле теперь представлены разные уведомления безопасности, которые будут отправлены. При полной реализации уведомления будут сохранены в базе данных; для упрощения в этом случае мы сохраняем их память.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. Замените в файле NotificationsController.cs код в определении класса **NotificationsController** на следующий. Этот компонент реализует способ безопасного получения уведомления устройством, а также способ (в обучающих целях) активации безопасного push-уведомления для устройств. Обратите внимание, что при отправке уведомления в центр уведомлений мы отправляем только необработанное уведомление с идентификатором уведомления (без фактического сообщения):
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Обратите внимание, что теперь метод `Post` не отправляет всплывающее уведомление. Он отправляет необработанное уведомление, которое содержит только идентификатор уведомления без конфиденциального содержимого. Также убедитесь, что закомментировали операцию отправки для платформ, для которых вы не настроили учетные данные в концентраторе уведомлений, поскольку они приведут к ошибкам.

1. После этого повторно развернем это приложение на веб-сайте Azure, чтобы сделать его доступным для всех устройств. Щелкните правой кнопкой мыши проект **AppBackend** и нажмите кнопку **Опубликовать**.
2. Выберите в качестве цели публикации веб-сайт Azure. Sign in with your Azure account and select an existing or new Website, and make a note of the **destination URL** property in the **Connection** tab. We will refer to this URL as your *backend endpoint* later in this tutorial. Щелкните **Опубликовать**.
