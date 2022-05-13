---
lab:
  title: 음성 인식 및 합성
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 4f65f068cab76299f838153d32a2d4e8979a1253
ms.sourcegitcommit: 3374bf6b03869daf624f3916bc34510fcbe580e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2022
ms.locfileid: "139088441"
---
# <a name="recognize-and-synthesize-speech"></a>음성 인식 및 합성

Azure Cognitive Service인 **Speech** 서비스에서는 다음과 같은 음성 관련 기능을 제공합니다.

- *음성 텍스트 변환* API - 음성 인식(가청 음성 단어를 텍스트로 변환)을 구현할 수 있습니다.
- *텍스트 음성 변환* API - 음성 합성(텍스트를 가청 음성으로 변환)을 구현할 수 있습니다.

이 연습에서는 두 API를 모두 사용하여 음성으로 시간을 안내하는 시계 애플리케이션을 구현합니다.

**참고**: 이 연습을 진행하려면 스피커/헤드폰이 있는 컴퓨터를 사용해야 합니다. 최상의 경험을 위해서는 마이크도 필요합니다. 일부 호스트 가상 환경에서는 로컬 마이크로부터 오디오를 캡처할 수 있지만, 이 기능이 작동하지 않거나 마이크가 아예 없는 경우에는 제공된 오디오 파일을 음성 입력으로 사용할 수 있습니다. 마이크를 사용하는지 아니면 오디오 파일을 사용하는지에 따라 서로 다른 옵션을 선택해야 하므로 주의하여 지침을 따르세요.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

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

## <a name="prepare-to-use-the-speech-service"></a>Speech 서비스 사용 준비

이 연습에서는 Speech SDK를 사용해 음성을 인식하고 합성하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

**참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **07-speech** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **speaking-clock** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Speech SDK 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. **speaking-clock** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Services 리소스용 인증 **키** 와 해당 리소스를 배포한 **위치** 를 포함하도록 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
4. **speaking-clock** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: speaking-clock.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Speech SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. **Main** 함수에서 구성 파일의 Cognitive Services 키 및 지역을 로드하는 코드가 이미 제공되어 있음을 확인합니다. 이러한 변수를 사용하여 Cognitive Services 리소스용 **SpeechConfig** 를 만들어야 합니다. **Speech 서비스 구성** 주석 아래에 다음 코드를 추가합니다.

    **C#**
    
    ```C#
    // Configure speech service
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    
    // Configure voice
    speechConfig.SpeechSynthesisVoiceName = "en-US-AriaNeural";
    ```
    
    **Python**
    
    ```Python
    # Configure speech service
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    print('Ready to use speech service in:', speech_config.region)
    ```

6. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

7. C#을 사용 중인 경우 비동기 메서드의 **await** 연산자 사용 관련 경고는 무시해도 됩니다. 뒷부분에서 해당 부분을 수정할 것입니다. 이 코드는 애플리케이션이 사용할 Speech 서비스 리소스의 지역을 표시합니다.

## <a name="recognize-speech"></a>음성 인식

Cognitive Services 리소스에서 Speech 서비스용 **SpeechConfig** 를 만들었으므로 **Speech-to-text** API를 사용하여 음성을 인식한 다음 텍스트로 필사할 수 있습니다.

### <a name="if-you-have-a-working-microphone"></a>작동하는 마이크가 있는 경우

1. 프로그램의 **Main** 함수에서 코드가 **TranscribeCommand** 함수를 사용해 음성 입력을 수락함을 확인합니다.
2. **TranscribeCommand** 함수의 **음성 인식 구성** 주석 아래에 다음의 적절한 코드를 추가하여 **SpeechRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 기본 시스템 마이크를 사용해 음성을 인식하고 필사할 수 있습니다.

    **C#**
    
    ```C#
    // Configure speech recognition
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    Console.WriteLine("Speak now...");
    ```
    
    **Python**
    
    ```Python
    # Configure speech recognition
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    print('Speak now...')
    ```

3. 이제 아래의 **필사된 명령을 처리하기 위한 코드 추가** 섹션으로 넘어갑니다.

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

3. **Main** 함수에서 코드가 **TranscribeCommand** 함수를 사용해 음성 입력을 수락함을 확인합니다. 그런 다음에 **TranscribeCommand** 함수의 **음성 인식 구성** 주석 아래에 다음의 적절한 코드를 추가하여 **SpeechRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 오디오 파일로부터 음성을 인식하고 필사할 수 있습니다.

    **C#**

    ```C#
    // Configure speech recognition
    string audioFile = "time.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech recognition
    audioFile = 'time.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

### <a name="add-code-to-process-the-transcribed-command"></a>필사된 명령을 처리하기 위한 코드 추가

1. **TranscribeCommand** 함수의 **음성 입력 처리** 주석 아래에 다음 코드를 추가하여 음성 입력을 청취합니다. 이때 명령을 반환하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

    **C#**
    
    ```C#
    // Process speech input
    SpeechRecognitionResult speech = await speechRecognizer.RecognizeOnceAsync();
    if (speech.Reason == ResultReason.RecognizedSpeech)
    {
        command = speech.Text;
        Console.WriteLine(command);
    }
    else
    {
        Console.WriteLine(speech.Reason);
        if (speech.Reason == ResultReason.Canceled)
        {
            var cancellation = CancellationDetails.FromResult(speech);
            Console.WriteLine(cancellation.Reason);
            Console.WriteLine(cancellation.ErrorDetails);
        }
    }
    ```

    **Python**
    
    ```Python
    # Process speech input
    speech = speech_recognizer.recognize_once_async().get()
    if speech.reason == speech_sdk.ResultReason.RecognizedSpeech:
        command = speech.text
        print(command)
    else:
        print(speech.reason)
        if speech.reason == speech_sdk.ResultReason.Canceled:
            cancellation = speech.cancellation_details
            print(cancellation.reason)
            print(cancellation.error_details)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. 마이크를 사용하는 경우에는 "what time is it?"라고 크고 분명하게 말합니다. 프로그램이 음성 입력을 필사한 다음 시간(코드를 실행 중인 컴퓨터의 현지 시간 기준. 현재 위치에서는 정확한 시간이 아닐 수도 있음)을 표시해야 합니다

    SpeechRecognizer에서는 말할 수 있는 시간이 약 5초입니다. 이 시간 동안 음성 입력이 감지되지 않으면 "No match" 결과가 생성됩니다.

    SpeechRecognizer에서 오류가 발생하면 "Cancelled" 결과가 생성됩니다. 그러면 애플리케이션의 코드가 오류 메시지를 표시합니다. 오류의 원인은 구성 파일의 지역이나 키가 정확하지 않기 때문일 가능성이 가장 높습니다.

## <a name="synthesize-speech"></a>음성 합성

음성 시계 애플리케이션은 음성 입력을 수락하지만 실제로 시간을 직접 말로 알려 주지는 않습니다. 이제 음성을 합성하는 코드를 추가하여 응답 방식을 수정해 보겠습니다.

1. 프로그램의 **Main** 함수에서 코드가 **TellTime** 함수를 사용하여 사용자에게 현재 시간을 알려 줌을 확인합니다.
2. **TellTime** 함수의 **음성 합성 구성** 주석 아래에 다음 코드를 추가하여 **SpeechSynthesizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 음성 출력을 생성할 수 있습니다.

    **C#**
    
    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```
    
    > **참고**: 기본 오디오 구성에서는 출력에 기본 시스템 오디오 디바이스를 사용하므로 **AudioConfig** 는 명시적으로 제공하지 않아도 됩니다. 오디오 출력을 파일로 리디렉션해야 하는 경우에는 파일 경로가 포함된 **AudioConfig** 를 사용하면 됩니다.

3. **TellTime** 함수의 **음성 출력 합성** 주석 아래에 다음 코드를 추가하여 음성 출력을 생성합니다. 이때 응답을 인쇄하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

    **C#**
    
    ```C#
    // Synthesize spoken output
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize spoken output
    speak = speech_synthesizer.speak_text_async(response_text).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

4. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

5. 메시지가 표시되면 마이크에 "what time is it"이라고 또렷하게 말합니다. 그러면 프로그램이 음성으로 시간을 알려 줍니다.

## <a name="use-a-different-voice"></a>다른 음성 사용

음성 시계 애플리케이션은 기본 음성을 사용하는데, 이 음성을 변경할 수 있습니다. Speech 서비스에서는 광범위한 *표준* 음성은 물론 인간의 음성과 더욱 비슷한 *신경망* 음성도 지원합니다. *사용자 지정* 음성을 만들 수도 있습니다.

> **참고**: 신경망 음성과 표준 음성 목록을 확인하려면 Speech 서비스 설명서의 [언어 및 음성 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)을 참조하세요.

1. **TellTime** 함수의 **음성 합성 구성** 주석 아래에서 코드를 다음과 같이 수정하여 **SpeechSynthesizer** 클라이언트를 만들기 전에 대체 음성을 지정합니다.

   **C#**

    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-LibbyNeural"; // change this
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = 'en-GB-LibbyNeural' # change this
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. 메시지가 표시되면 마이크에 "what time is it"이라고 또렷하게 말합니다. 그러면 프로그램이 지정된 음성으로 시간을 알려 줍니다.

## <a name="use-speech-synthesis-markup-language"></a>Speech Synthesis Markup Language 사용

SSML(Speech Synthesis Markup Language)을 사용하면 XML 기반 형식을 통해 음성 합성 방식을 사용자 지정할 수 있습니다.

1. **TellTime** 함수에서 **음성 출력 합성** 현재 주석 아래에 있는 모든 코드를 다음 코드로 바꿉니다(**응답 인쇄** 주석 아래의 코드는 그대로 유지).

   **C#**

    ```C#
    // Synthesize spoken output
    string responseSsml = $@"
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
            <voice name='en-GB-LibbyNeural'>
                {responseText}
                <break strength='weak'/>
                Time to end this lab!
            </voice>
        </speak>";
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakSsmlAsync(responseSsml);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**
    
    ```Python
    # Synthesize spoken output
    responseSsml = " \
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'> \
            <voice name='en-GB-LibbyNeural'> \
                {} \
                <break strength='weak'/> \
                Time to end this lab! \
            </voice> \
        </speak>".format(response_text)
    speak = speech_synthesizer.speak_ssml_async(responseSsml).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. 메시지가 표시되면 마이크에 "what time is it"이라고 또렷하게 말합니다. 프로그램이 SSML에 지정된 음성으로 시간을 알려 준 다음(SpeechConfig에 지정된 음성이 재정의됨) 잠시 후에 종료하려면 "Time to end this lab!"을 말하라고 합니다.

## <a name="more-information"></a>추가 정보

**음성 텍스트 변환** 및 **텍스트 음성 변환** API 사용에 대한 자세한 내용은 [음성 텍스트 변환 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) 및 [텍스트 음성 변환 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech)를 참조하세요.
