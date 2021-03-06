---
title: Руководство по шаблону приложения микроцентра исполнения заказов | Документация Майкрософт
description: Руководство по шаблону приложения микроцентра исполнения заказов для IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026227"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Руководство. Развертывание и использование шаблона приложения микроцентра исполнения заказов

В этом руководстве, мы используем шаблон приложения ***Микроцентр исполнения заказов*** Azure IoT Central, чтобы продемонстрировать создание решения для розничной торговли. Вы узнаете как развернуть шаблон MFC, что входит в комплект поставки, и что можно будет сделать дальше.

В этом руководстве описано следующее: 
> [!div class="checklist"]
> * Создание приложения розничной торговли с помощью шаблона Azure IoT Central **Микроцентр исполнения заказов**
> * Знакомство с приложением 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этой серией учебников вам потребуется следующее:
* Подписка Azure. При необходимости можно использовать бесплатную пробную версию в течение семи дней. Если у вас еще нет подписки, создайте ее на [странице регистрации в Azure](https://aka.ms/createazuresubscription);

## <a name="create-an-application"></a>Создание приложения 
В этом разделе вы создадите новое приложение Azure IoT Central на основе шаблона. Это приложение будет использоваться на всем протяжении серии учебников для создания полного решения.

Чтобы создать приложение Azure IoT Central, сделайте следующее:

1. Перейдите на веб-сайт [диспетчера приложений в Azure IoT Central](https://aka.ms/iotcentral).
1. Если у вас есть подписка Azure, выполните вход с учетными данными для доступа к ней. Если ее нет, используйте учетную запись Майкрософт:

   ![Ввод учетной записи организации](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Чтобы начать создание приложения Azure IoT Central, выберите **Новое приложение**.

1. Выберите **Розничная торговля**.  На странице "Розничная торговля" отображается несколько шаблонов розничных приложений.

Чтобы создать новое приложение для микроцентра исполнения заказов, которое использует предварительные версии функций, сделайте следующее.  
1. Выберите шаблон приложения **Микроцентр исполнения заказов**. Этот шаблон включает шаблоны для всех устройств, используемых в руководстве. Шаблон также предоставляет панель оператора для мониторинга условий в вашем центре выполнения заказов, а также условий для роботизированных операторов. 

    > [!div class="mx-imgBorder"]
    > ![Тип приложения микроцентра выполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Выберите **понятное имя** для приложения.  Шаблон приложения основан на вымышленной компании Northwind Traders. 

    > [!NOTE]
    > Если вы используете понятное **имя приложения**, все равно необходимо использовать уникальное значение для **URL-адреса** приложения.

1. Если у вас есть подписка Azure, укажите *каталог, подписку Azure и регион*. Если у вас нет подписки, вы можете включить **семидневную бесплатную пробную версию** и указать необходимые контактные данные.  

    Дополнительные сведения о каталогах и подписках см. в статье [Создание приложения Azure IoT Central](../preview/quick-deploy-iot-central.md).

1. Нажмите кнопку **создания**.

> [!div class="mx-imgBorder"]
> ![Создание приложения микроцентра выполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>ознакомиться с этим приложением. 

### <a name="dashboard"></a>Панель мониторинга 

После успешного развертывания шаблона приложения, сначала вы окажетесь на **панели мониторинга микроцентра исполнения заказов Northwind Traders**. Northwind Trader — это вымышленный розничный продавец, который управляет микроцентром исполнения заказов в этом приложении IoT Central. На этой панели мониторинга оператора вы увидите сведения и данные телеметрии об устройствах в этом шаблоне вместе с набором команд, заданий и действий, которые вы можете выполнить. Панель мониторинга логически разделена на две части (левую и правую). Слева вы можете следить за состоянием среды в структуре выполнения, а справа — за состоянием роботизированного оператора в рамках этого средства.  

На панели мониторинга можно:
   * Просматривать сведения о телеметрии устройства, такие ​​как количество извлечений, и обработанных заказов, а также такие свойства, как состояние системы структуры и т. д.  
   * Просмотреть **план этажа** и расположение роботизированных операторов в структуре выполнения.
   * Активируйте такие команды, как сброс системы управления, обновление встроенного ПО оператора, изменение конфигурации сети и т. д.

> [!div class="mx-imgBorder"]
> ![Панель мониторинга микроцентра исполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * См. пример панели мониторинга, которую оператор может использовать для наблюдения за условиями в центре выполнения. 
   * Мониторинг работоспособности полезных данных, выполняемых на устройстве шлюза в центре выполнения.    

> [!div class="mx-imgBorder"]
> ![Панель мониторинга микроцентра исполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Шаблон устройства
Если щелкнуть вкладку "Шаблоны устройств", вы увидите, что в шаблоне есть два различных типа устройств: 
   * **Роботизированный оператор**: этот шаблон устройства представляет определение действующего роботизированного оператора, который был развернут в структуре выполнения и выполняет определенные операции хранения и извлечения. Если вы нажмете на шаблон, то увидите, что робот отправляет данные устройства, например, температуру, расположение осей и свойства, такие как состояние роботизированного оператора и т. д. 
   * **Мониторинг состояния структуры**: этот шаблон устройства представляет собой коллекцию устройств, которая позволяет отслеживать состояние среды, а также устройство шлюза, на котором размещены различные пограничные рабочие нагрузки для обеспечения работы центра выполнения. Устройство отправляет данные телеметрии, такие как температура, количество извлечений и заказов и т. д., в дополнение к состоянию и работоспособности вычислительных рабочих нагрузок, выполняемых в вашей среде. 

> [!div class="mx-imgBorder"]
> ![Шаблоны устройств микроцентра исполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Если щелкнуть вкладку "Группы устройств", вы увидите, что для этих шаблонов устройств автоматически созданы группы устройств.

## <a name="rules"></a>Правила
При переходе на вкладку правил вы увидите пример правила, которое существует в шаблоне приложения для отслеживания температурных условий для роботизированного оператора. Это правило можно использовать, чтобы предупредить оператора, если конкретный робот на объекте перегревается и его необходимо отключить для обслуживания. 

Используйте пример правила как помощь для определения правил, более подходящих для бизнес-функций.

   - **Роботизированный оператор слишком теплый**: это правило срабатывает, если роботизированный оператор достигает температурного порога в течение определенного периода времени. 

> [!div class="mx-imgBorder"]
> ![Правила микроцентра исполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь в дальнейшем использовать это приложение, удалите шаблон приложения, выбрав **Administration** (Администрирование)  > **Application settings** (Параметры приложения) и щелкнув **Delete** (Удалить).

> [!div class="mx-imgBorder"]
> ![Очистка приложения для микроцентра выполнения заказов](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения см. [Архитектура для микроцентра исполнения заказов](./architecture-micro-fulfillment-center-pnp.md)
* Узнайте больше о других [шаблонах IoT Central для розничной торговли](./overview-iot-central-retail-pnp.md).
* См. сведения об [IoT Central](../preview/overview-iot-central.md).