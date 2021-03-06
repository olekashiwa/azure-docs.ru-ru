---
title: Данные конфигурации в масштабе — служба автоматизации Azure
description: Узнайте, как настроить масштабируемые данные для конфигурации состояния в службе автоматизации Azure.
keywords: dsc,powershell,конфигурация,установка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028290"
---
# <a name="configuration-data-at-scale"></a>Использование данных конфигурации в нужном масштабе

> Область применения: Windows PowerShell 5,1

Управление сотнями или тысячами серверов может оказаться сложной задачей.
Клиенты предоставили отзыв о том, что наиболее сложный аспект фактически управляет [данными конфигурации](/powershell/scripting/dsc/configurations/configdata).
Упорядочение информации по логическим конструкциям, таким как расположение, тип и среда.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-datum"></a>Проект сообщества: база

Решение, обслуживаемое сообществом [Datum](https://github.com/gaelcolas/Datum) , было создано для решения этой проблемы.
База данных основана на замечательных идеях от других платформ управления конфигурацией и реализует тот же тип решения для PowerShell DSC.
Информация [организована в текстовые файлы](https://github.com/gaelcolas/Datum#3-intended-usage) на основе логических идей.
Пример будет следующим:

- Параметры, которые должны применяться глобально
- Параметры, которые должны применяться ко всем серверам в расположении
- Параметры, которые должны применяться ко всем серверам баз данных
- Параметры отдельных серверов

Эти сведения организованы в предпочтительный формат файла (JSON, YAML или PSD1).
Затем предоставляются командлеты для создания файлов данных конфигурации путем [консолидации информации](https://github.com/gaelcolas/Datum#datum-tree) из каждого файла в единое представление сервера или роли сервера.

После создания файлов данных их можно использовать с [сценариями конфигурации DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) для создания MOF-файлов и [передачи MOF-файлов в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.

Чтобы испытать версию Datum, посетите [коллекция PowerShell](https://www.powershellgallery.com/packages/datum/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaconfig)
