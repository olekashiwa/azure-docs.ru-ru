---
title: Краткое руководство. Пользовательский голосовой помощник, C# (UWP) в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью пакета средства разработки программного обеспечения службы "Речь" в Cognitive Services. Вы подключите свое клиентское приложение к ранее созданному боту Bot Framework, который настроен для использования канала "Речь Direct Line". Приложение создается с использованием пакета SDK службы "Речь" для NuGet и Microsoft Visual Studio 2019.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: af9395b8f75a9097d4449571fd54be6259a7b9f3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119776"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>Краткое руководство. Создание голосового помощника с помощью пакета SDK службы "Речь" на UWP

Также доступны краткие руководства по [распознаванию](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp), [синтезу](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) и [переводу](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) речи.

Из этой статьи вы узнаете, как разработать приложение C# для универсальной платформы Windows с помощью [пакета SDK службы "Речь"](speech-sdk.md). Программа подключится к ранее созданному и настроенному боту, чтобы включить из клиентского приложения интерфейс голосового помощника. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2019 (любого выпуска).

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Ключ подписки Azure для службы "Речь". [Получите бесплатно](get-started.md) или создайте его на [портале Azure](https://portal.azure.com).
* Ранее созданный бот, настроенный с помощью[канала "Речь Direct Line"](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Ознакомьтесь со [списком поддерживаемых регионов для голосовых помощников](regions.md#voice-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

## <a name="optional-get-started-fast"></a>Необязательное действие: Быстрое начало работы

В этом кратком пошаговом руководстве описано, как создать клиентское приложение для подключения к боту с поддержкой речи. Если вы хотите сразу приступить к работе, полноценный, готовый к компиляции исходный код, используемый в этом кратком руководстве, доступен в [Примерах пакета SDK для распознавания речи](https://aka.ms/csspeech/samples) в папке `quickstart`.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

Теперь добавьте XAML-код, определяющий пользовательский интерфейс приложения, и добавьте реализацию C# кода программной части.

### <a name="xaml-code"></a>Код XAML

Сначала вы создадите пользовательский интерфейс приложения, добавив код XAML:

1. Откройте `MainPage.xaml` в **обозревателе решений**.

1. В представлении XAML конструктора замените все содержимое следующим фрагментом кода:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

Конструктор обновляется для отображения пользовательского интерфейса приложения.

### <a name="c-code-behind-source"></a>Источник кода C# программной части

Затем добавьте источник кода программной части, чтобы приложение правильно работало. Источник кода программной части включает в себя:

- `using` инструкции для `Speech` и `Speech.Dialog` пространства имен
- простая реализация доступа к микрофону с привязкой к обработчику кнопки;
- базовые вспомогательные функции пользовательского интерфейса для представления сообщений и ошибок в приложении;
- целевая точка для пути кода инициализации, который будет добавлен позже;
- вспомогательное приложение для воспроизведения текста, преобразованного в речь (без поддержки потоковой передачи данных);
- пустой обработчик кнопки воспроизведения, который будет заполнен позже.

Чтобы добавить источник кода программной части, выполните следующие действия:

1. В **Обозревателе решений** откройте исходный файл кода программной части `MainPage.xaml.cs`. (Он сгруппирован в `MainPage.xaml`.)

1. Замените содержимое этого файла приведенным ниже фрагментом кода:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Добавьте следующий фрагмент кода в текст метода `InitializeDialogServiceConnector`. Этот код создает `DialogServiceConnector` с помощью сведений о подписке.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Ознакомьтесь со [списком поддерживаемых регионов для голосовых помощников](regions.md#voice-assistants) и убедитесь, что ваши ресурсы развернуты в одном из этих регионов.

   > [!NOTE]
   > Для получения сведений о настройке бота и секрете канала см. документацию по Bot Framework для [канала "Речь Direct Line"](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Замените строки `YourChannelSecret`, `YourSpeechSubscriptionKey` и `YourServiceRegion` на собственные значения для вашего бота, речевой подписки и [региона](regions.md).

1. Добавьте следующий фрагмент кода в конец текста метода `InitializeDialogServiceConnector`. Этот код настраивает обработчики событий, на которые полагается `DialogServiceConnector` для передачи информации о деятельности ботов, результатах распознавания речи и другой информации.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. В текст метода `ListenButton_ButtonClicked` в классе `MainPage` добавьте следующее. Этот код настраивается `DialogServiceConnector` для прослушивания, так как вы уже установили конфигурацию и зарегистрировали обработчики событий.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. В строке меню выберите **Файл** > **Сохранить все**, чтобы сохранить изменения.

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Теперь все готово для сборки и тестирования приложения.

1. В строке меню выберите **Сборка** > **Построить решение**, чтобы создать приложение. Теперь код должен компилироваться без ошибок.

1. Выберите **Отладка** > **Начать отладку**(или нажмите клавишу **F5**), чтобы запустить приложение. Откроется окно **helloworld**.

   ![Пример приложения голосового помощника UWP на C# — краткое руководство](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Выберите **Включить микрофон**, а когда появится запрос на разрешение доступа, выберите **Да**.

   ![Запрос на разрешение доступа к микрофону](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Щелкните **Talk to your bot** (Разговор с ботом) и произнесите фразу или предложение на английском языке в микрофон устройства. Ваша речь передастся в канал "Речь Direct Line" и преобразуется в текст, который появится в том же окне.
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание и развертывание простого бота](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>См. также раздел

- [Сведения о голосовых помощниках](voice-assistants.md)
- [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
- [Пользовательские ключевые слова](speech-devices-sdk-create-kws.md)
- [Подключение бота к каналу Direct Line Speech (предварительная версия)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)
