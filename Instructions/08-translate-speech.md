---
lab:
  title: 음성 번역
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 45c8d0d31bee5901247b22917d8dbad8c587a0bd
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2022
ms.locfileid: "137819444"
---
# <a name="translate-speech"></a>음성 번역

**Speech** 서비스에 포함된 **Speech Translation** API를 사용하면 음성 언어를 번역할 수 있습니다. 예를 들어 현지어를 모르는 곳을 여행할 때 사용할 수 있는 번역기 애플리케이션을 개발하려는 등의 경우에 이 API를 사용할 수 있습니다. 그들은 “역은 어디에 있습니까?” 와 같은 문구를 말할 수 있을 것입니다. 또는 자신의 언어로 “약국을 찾아야”하고 현지 언어로 번역해야 합니다.

**참고**: 이 연습을 진행하려면 스피커/헤드폰이 있는 컴퓨터를 사용해야 합니다. 최상의 경험을 위해서는 마이크도 필요합니다. 일부 호스트 가상 환경에서는 로컬 마이크로부터 오디오를 캡처할 수 있지만, 이 기능이 작동하지 않거나 마이크가 아예 없는 경우에는 제공된 오디오 파일을 음성 입력으로 사용할 수 있습니다. 마이크를 사용하는지 아니면 오디오 파일을 사용하는지에 따라 서로 다른 옵션을 선택해야 하므로 주의하여 지침을 따르세요.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 **Cognitive Services** 리소스가 아직 없으면 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 키 중 하나, 그리고 이 페이지에서 서비스가 프로비전된 위치가 필요합니다.

## <a name="prepare-to-use-the-speech-translation-service"></a>Speech Translation 서비스 사용 준비

이 연습에서는 Speech SDK를 사용해 음성을 인식, 번역 및 합성하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **08-speech-translation** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **translator** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Speech SDK 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. **translator** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Services 리소스용 인증 **키** 와 해당 리소스를 배포한 **위치** 를 포함하도록 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
4. **translator** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: translator.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Speech SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. **Main** 함수에서 구성 파일의 Cognitive Services 키 및 지역을 로드하는 코드가 이미 제공되어 있음을 확인합니다. 이러한 변수를 사용하여 Cognitive Services 리소스용 **SpeechTranslationConfig** 를 만들어야 합니다. SpeechTranslationConfig를 사용하여 음성 입력을 번역합니다. **번역 구성** 주석 아래에 다음 코드를 추가합니다.

    **C#**
    
    ```C#
    // Configure translation
    translationConfig = SpeechTranslationConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    translationConfig.SpeechRecognitionLanguage = "en-US";
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("es");
    translationConfig.AddTargetLanguage("hi");
    Console.WriteLine("Ready to translate from " + translationConfig.SpeechRecognitionLanguage);
    ```
    
    **Python**
    
    ```Python
    # Configure translation
    translation_config = speech_sdk.translation.SpeechTranslationConfig(cog_key, cog_region)
    translation_config.speech_recognition_language = 'en-US'
    translation_config.add_target_language('fr')
    translation_config.add_target_language('es')
    translation_config.add_target_language('hi')
    print('Ready to translate from',translation_config.speech_recognition_language)
    ```

6. **SpeechTranslationConfig** 를 사용하여 음성을 텍스트로 번역하며, **SpeechConfig** 도 사용하여 번역을 음성으로 합성합니다. **음성 구성** 주석 아래에 다음 코드를 추가합니다.

    **C#**
    
    ```C#
    // Configure speech
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    ```
    
    **Python**
    
    ```Python
    # Configure speech
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    ```

7. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

8. C#을 사용 중인 경우 비동기 메서드의 **await** 연산자 사용 관련 경고는 무시해도 됩니다. 뒷부분에서 해당 부분을 수정할 것입니다. 이 코드는 en-US 텍스트를 번역할 수 있다는 메시지를 표시합니다. ENTER 키를 눌러 프로그램을 종료합니다.

## <a name="implement-speech-translation"></a>음성 번역 구현

Cognitive Services 리소스에서 Speech 서비스용 **SpeechTranslationConfig** 를 만들었으므로 **Speech Translation** API를 사용하여 음성을 인식한 다음 번역할 수 있습니다.

### <a name="if-you-have-a-working-microphone"></a>작동하는 마이크가 있는 경우

1. 프로그램의 **Main** 함수에서 코드가 **Translate** 함수를 사용해 음성 입력을 번역함을 확인합니다.
2. **Translate** 함수의 **음성 번역** 주석 아래에 다음 코드를 추가하여 **TranslationRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 입력용 기본 시스템 마이크를 사용해 음성을 인식하고 번역할 수 있습니다.

    **C#**
    
    ```C#
    // Translate speech
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Speak now...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Speak now...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **참고**: 애플리케이션의 코드를 한 번만 호출하면 입력이 3개 언어로 번역됩니다. 출력에는 특정 언어의 번역만 표시되지만 결과의 **translations** 컬렉션에서 대상 언어 코드를 지정하면 원하는 번역을 검색할 수 있습니다.

3. 이제 아래의 **프로그램 실행** 섹션으로 넘어갑니다.

### <a name="alternatively-use-audio-input-from-a-file"></a>또는 파일에서 오디오 입력 사용

1. 터미널 차에서 다음 명령을 입력하여 오디오 파일을 재생하는 데 사용할 수 있는 라이브러리를 설치합니다.

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

2. 프로그램의 코드 파일에서, 기존 네임스페이스 가져오기 아래에 다음 코드를 추가하여 방금 설치한 라이브러리를 가져옵니다.

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

3. 프로그램의 **Main** 함수에서 코드가 **Translate** 함수를 사용해 음성 입력을 번역함을 확인합니다. 그런 다음에 **Translate** 함수의 **음성 번역** 주석 아래에 다음 코드를 추가하여 **TranslationRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 파일로부터 음성을 인식하고 번역할 수 있습니다.

    **C#**
    
    ```C#
    // Translate speech
    string audioFile = "station.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Getting speech from file...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audioFile = 'station.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Getting speech from file...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **참고**: 애플리케이션의 코드를 한 번만 호출하면 입력이 3개 언어로 번역됩니다. 출력에는 특정 언어의 번역만 표시되지만 결과의 **translations** 컬렉션에서 대상 언어 코드를 지정하면 원하는 번역을 검색할 수 있습니다.

### <a name="run-the-program"></a>프로그램 실행

1. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

2. 메시지가 표시되면 유효한 언어 코드(*fr*, *es*, 또는 *hi*)를 입력한 다음 마이크를 사용하는 경우 명확하게 말하고 “역은 어디에 있나요?”라고 말합니다. 또는 해외 여행 시 사용할 수 있는 다른 구문입니다. 그러면 프로그램이 음성 입력을 필사한 다음 지정한 언어(프랑스어, 스페인어 또는 힌디어)로 번역합니다. 이 프로세스를 반복하여 음성을 애플리케이션이 지원하는 각 언어로 번역해 봅니다. 프로세스를 완료한 후 Enter 키를 눌러 프로그램을 종료합니다.

    > **참고**: TranslationRecognizer에서는 말할 수 있는 시간이 약 5초입니다. 이 시간 동안 음성 입력이 감지되지 않으면 "No match" 결과가 생성됩니다.
    >
    > 언어 인코딩 문제로 인해 힌디어 번역이 콘솔 창에 올바르게 표시되지 않는 경우도 있습니다.

## <a name="synthesize-the-translation-to-speech"></a>번역을 음성으로 합성

지금까지 애플리케이션은 음성 입력을 텍스트로 번역했습니다. 여행 중에 도움을 요청할 때는 텍스트 번역으로도 충분할 수 있습니다. 그러나 애플리케이션이 적절한 음성으로 번역을 '말해' 준다면 더 효율적일 것입니다.

1. **Translate** 함수의 **번역 합성** 주석 아래에 다음 코드를 추가합니다. 이 코드는 **SpeechSynthesizer** 클라이언트를 사용해 번역을 음성으로 합성하여 기본 스피커에서 재생합니다.

    **C#**
    
    ```C#
    // Synthesize translation
    var voices = new Dictionary<string, string>
                    {
                        ["fr"] = "fr-FR-HenriNeural",
                        ["es"] = "es-ES-ElviraNeural",
                        ["hi"] = "hi-IN-MadhurNeural"
                    };
    speechConfig.SpeechSynthesisVoiceName = voices[targetLanguage];
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(translation);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize translation
    voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
    }
    speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    speak = speech_synthesizer.speak_text_async(translation).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

3. 메시지가 표시되면 유효한 언어 코드(*fr*, *es*, 또는 *hi*)를 입력한 후 해외 여행 시에 사용할 수 있는 구를 마이크에 또박또박 말합니다. 그러면 프로그램이 음성 입력을 필사한 다음 음성 번역을 응답으로 제공합니다. 이 프로세스를 반복하여 음성을 애플리케이션이 지원하는 각 언어로 번역해 봅니다. 프로세스를 완료한 후 Enter 키를 눌러 프로그램을 종료합니다.

    > **참고** 이 예제에서는 **SpeechTranslationConfig** 를 사용하여 음성을 텍스트로 번역한 다음 **SpeechConfig** 를 사용하여 번역을 음성으로 합성했습니다. 실제로는 **SpeechTranslationConfig** 를 사용해 번역을 직접 합성할 수 있습니다. 하지만 이 방식은 한 가지 언어로 번역할 때만 사용 가능하며, 음성 번역은 오디오 스트림으로 생성되어 스피커에서 바로 재생되는 대신 대개 파일로 저장됩니다.

## <a name="more-information"></a>추가 정보

**Speech Translation** API를 사용하는 방법에 대한 자세한 내용은 [Speech Translation 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-translation)를 참조하세요.
