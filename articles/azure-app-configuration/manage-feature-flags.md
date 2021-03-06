---
title: Руководство. Управление флагами компонентов с помощью службы "Конфигурация приложений Azure"
titleSuffix: Azure App Configuration
description: Из этого руководства вы узнаете, как управлять флагами компонентов отдельно от приложения с помощью Конфигурации приложений Azure.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899369"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Руководство. Управление флагами компонентов в конфигурации приложений Azure

Можно хранить все флаги компонентов в конфигурации приложений Azure и управлять ими в одном месте. Конфигурация приложений предоставляет портал **Диспетчер компонентов** с пользовательским интерфейсом. Он разработан специально для флагов компонентов. Кроме того, Конфигурация приложений имеет встроенную поддержку схемы данных флага компонентов .NET Core.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Определяйте и управляйте флагами компонентов в конфигурации приложений.
> * Осуществляйте доступ к флагам компонентов из приложения.

## <a name="create-feature-flags"></a>Создание флагов компонентов

Диспетчер компонентов на портале Azure для Конфигурации приложений предоставляет пользовательский интерфейс для создания флагов компонентов, которые используются в приложении, а также управления ими.

Чтобы добавить флаг компонента, сделайте следующее:

1. Выберите **Диспетчер компонентов** >  **+Добавить**, чтобы добавить флаг компонента.

    ![Список флагов компонентов](./media/azure-app-configuration-feature-flags.png)

1. Введите уникальное имя ключа для флага компонентов. Это имя необходимо для ссылки на флаг в коде.

1. При необходимости укажите описание флага компонента.

1. Установите начальное состояния флага компонентов. Состояние обычно может иметь значение *Отключено* или *Включено*. Состояние *Включено* изменится на *Условное*, если вы добавите к флагу компонента фильтр.

    ![Создание флага компонента](./media/azure-app-configuration-feature-flag-create.png)

1. Если указано состояние *Включено*, выберите **+Добавить фильтр**, чтобы указать дополнительные условия для уточнения состояния. Введите встроенный или пользовательский ключ фильтра и выберите **+Добавить параметр**, чтобы связать один или несколько параметров с фильтром. Встроенные фильтры включают в себя:

    | Клавиши | параметры JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0–100 процентов} |
    | Microsoft.TimeWindow | {"Start": время в формате UTC, "End": время в формате UTC} |

    ![Фильтр флагов компонентов](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Обновление состояния флага компонентов

Чтобы изменить значение состояния для флага компонента, сделайте следующее:

1. Выберите **Диспетчер функций**.

1. Справа от флага компонента, который нужно изменить, щелкните значок многоточия ( **...** ) и выберите **Изменить**.

1. Задайте новое состояние флага компонента.

## <a name="access-feature-flags"></a>Доступ к флагам компонента

Флаги компонентов, созданные диспетчером компонентов, хранятся и извлекаются в виде обычных значений ключей. Они хранятся со специальным префиксом пространства имен (`.appconfig.featureflag`). Базовые значения ключей можно просмотреть с помощью обозревателя конфигураций. Ваше приложение может получить эти значения с помощью поставщиков конфигураций в Конфигурации приложений, пакетов SDK, расширений командной строки и REST API.

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как управлять флагами компонентов и их состоянием с помощью Конфигурации приложений. См. подробнее о поддержке управления компонентами в Конфигурации приложений и ASP.NET Core:

* [Использование флагов компонентов в приложении ASP.NET Core](./use-feature-flags-dotnet-core.md)
