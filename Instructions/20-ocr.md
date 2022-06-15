---
lab:
  title: 이미지에서 텍스트 읽기
  module: Module 11 - Reading Text in Images and Documents
ms.openlocfilehash: 1199e4e4f44a98fc5f900fa1ad021384b56f0c2b
ms.sourcegitcommit: e242452e8125a2622093980048f1e2cacb8b893d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2022
ms.locfileid: "145757493"
---
# <a name="read-text-in-images"></a>이미지에서 텍스트 읽기

OCR(광학 인식)은 이미지와 문서에서 텍스트 읽기를 처리하는 Computer Vision 하위 서비스입니다. **Computer Vision** 서비스에서는 텍스트 읽기용 API 2개를 제공합니다. 이 연습에서는 해당 API를 살펴봅니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 과정용 코드 리포지토리를 아직 복제하지 않았으면 복제해야 합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

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
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 이 페이지에 표시되는 키 중 하나와 엔드포인트가 필요합니다.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Computer Vision SDK 사용 준비

이 연습에서는 Computer Vision SDK를 사용해 텍스트를 읽는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **20-ocr** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **read-text** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Computer Vision SDK 패키지를 설치합니다.

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```

3. **read-text** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Service 리소스용 **엔드포인트** 및 인증 **키** 를 반영하여 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
4. **read-text** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: read-text.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Computer Vision SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

**C#**

```C#
// import namespaces
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
```

**Python**

```Python
# import namespaces
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
```

5. 클라이언트 애플리케이션용 코드 파일의 **Main** 함수에서 구성 설정 로드를 위한 코드가 제공되어 있음을 확인합니다. 그런 다음 **Computer Vision 클라이언트 인증** 주석을 찾습니다. 그 후에 이 주석 아래에 다음 언어별 코드를 추가하여 Computer Vision 클라이언트 개체를 만들고 인증합니다.

**C#**

```C#
// Authenticate Computer Vision client
ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
cvClient = new ComputerVisionClient(credentials)
{
    Endpoint = cogSvcEndpoint
};
```

**Python**

```Python
# Authenticate Computer Vision client
credential = CognitiveServicesCredentials(cog_key) 
cv_client = ComputerVisionClient(cog_endpoint, credential)
```
    
## <a name="use-the-ocr-api"></a>OCR API 사용

**OCR** API, 즉 광학 인식 API는 *.jpg*, *.png*, *.gif* 및 *.bmp* 형식 이미지에 인쇄되어 있는 소량~중간 정도의 텍스트를 읽는 작업용으로 최적화되어 있습니다. 이 API는 광범위한 언어를 지원하며, 이미지의 텍스트를 읽을 수 있을 뿐 아니라 각 텍스트 영역의 방향을 확인하여 이미지 기준 텍스트 회전 각도 관련 정보도 반환할 수 있습니다.

1. 애플리케이션용 코드 파일의 **Main** 함수에서 사용자가 메뉴 옵션 **1** 을 선택하면 실행되는 코드를 살펴봅니다. 이 코드는 **GetTextOcr** 함수를 호출하여 이미지 파일의 경로를 전달합니다.
2. **read-text/images** 폴더에서 **Lincoln.jpg** 를 열어 코드가 처리할 이미지를 표시합니다.
3. 코드 파일로 돌아와서 **GetTextOcr** 함수를 찾은 다음 콘솔에 메시지를 인쇄하는 기존 코드 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Use OCR API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var ocrResults = await cvClient.RecognizePrintedTextInStreamAsync(detectOrientation:false, image:imageData);

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Magenta, 3);

    foreach(var region in ocrResults.Regions)
    {
        foreach(var line in region.Lines)
        {
            // Show the position of the line of text
            int[] dims = line.BoundingBox.Split(",").Select(int.Parse).ToArray();
            Rectangle rect = new Rectangle(dims[0], dims[1], dims[2], dims[3]);
            graphics.DrawRectangle(pen, rect);

            // Read the words in the line of text
            string lineText = "";
            foreach(var word in line.Words)
            {
                lineText += word.Text + " ";
            }
            Console.WriteLine(lineText.Trim());
        }
    }

    // Save the image with the text locations highlighted
    String output_file = "ocr_results.jpg";
    image.Save(output_file);
    Console.WriteLine("Results saved in " + output_file);
}
```

**Python**

```Python
# Use OCR API to read text in image
with open(image_file, mode="rb") as image_data:
    ocr_results = cv_client.recognize_printed_text_in_stream(image_data)

# Prepare image for drawing
fig = plt.figure(figsize=(7, 7))
img = Image.open(image_file)
draw = ImageDraw.Draw(img)

# Process the text line by line
for region in ocr_results.regions:
    for line in region.lines:

        # Show the position of the line of text
        l,t,w,h = list(map(int, line.bounding_box.split(',')))
        draw.rectangle(((l,t), (l+w, t+h)), outline='magenta', width=5)

        # Read the words in the line of text
        line_text = ''
        for word in line.words:
            line_text += word.text + ' '
        print(line_text.rstrip())

# Save the image with the text locations highlighted
plt.axis('off')
plt.imshow(img)
outputfile = 'ocr_results.jpg'
fig.savefig(outputfile)
print('Results saved in', outputfile)
```

4. **GetTextOcr** 함수에 추가한 코드를 살펴봅니다. 이 코드는 이미지 파일에서 인쇄된 텍스트 영역을 감지한 다음 각 영역에서 텍스트 줄을 추출하고 이미지의 텍스트 줄 위치를 강조 표시합니다. 그런 후에 각 줄의 단어를 추출하여 인쇄합니다.
5. 변경 내용을 저장하고 **read-text** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

이제 C# 출력에 **await** 연산자를 사용하는 비동기 함수 관련 경고가 표시될 수 있습니다. 이러한 경고는 이러한 메시지는 무시해도 됩니다.

**Python**

```
python read-text.py
```

6. 메시지가 표시되면 **1** 을 입력하고 출력을 살펴봅니다. 출력은 이미지에서 추출된 텍스트입니다.
7. 코드 파일과 같은 폴더에 생성된 **ocr_results.jpg** 파일을 표시하여 이미지에서 주석이 추가된 텍스트 줄을 확인합니다.

## <a name="use-the-read-api"></a>Read API 사용

**Read** API는 OCR API에 비해 최신 버전의 텍스트 인식 모델을 사용하며, 텍스트가 많이 포함된 큰 이미지에서 텍스트를 인식하는 성능이 더 우수합니다. 또한 *.pdf* 파일에서 텍스트를 추출하는 작업도 지원하며, 여러 언어의 인쇄된 텍스트와 필기 텍스트를 모두 인식할 수 있습니다.

**Read** API는 비동기 작업 모델을 사용합니다. 이 모델에서는 텍스트 인식 시작 요청이 제출되며, 해당 요청에서 반환된 작업 ID를 이후 작업에서 사용하여 진행률을 확인하고 결과를 검색할 수 있습니다.

1. 애플리케이션용 코드 파일의 **Main** 함수에서 사용자가 메뉴 옵션 **2** 를 선택하면 실행되는 코드를 살펴봅니다. 이 코드는 **GetTextRead** 함수를 호출하여 PDF 문서 파일의 경로를 전달합니다.
2. **read-text/images** 폴더에서 **Rome.pdf** 를 마우스 오른쪽 단추로 클릭하고 **파일 탐색기에 표시** 를 선택합니다. 그런 다음 파일 탐색기에서 PDF 파일을 열어 표시합니다.
3. Visual Studio Code의 코드 파일로 돌아와서 **GetTextRead** 함수를 찾은 다음 콘솔에 메시지를 인쇄하는 기존 코드 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Use Read API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var readOp = await cvClient.ReadInStreamAsync(imageData);

    // Get the async operation ID so we can check for the results
    string operationLocation = readOp.OperationLocation;
    string operationId = operationLocation.Substring(operationLocation.Length - 36);

    // Wait for the asynchronous operation to complete
    ReadOperationResult results;
    do
    {
        Thread.Sleep(1000);
        results = await cvClient.GetReadResultAsync(Guid.Parse(operationId));
    }
    while ((results.Status == OperationStatusCodes.Running ||
            results.Status == OperationStatusCodes.NotStarted));

    // If the operation was successfuly, process the text line by line
    if (results.Status == OperationStatusCodes.Succeeded)
    {
        var textUrlFileResults = results.AnalyzeResult.ReadResults;
        foreach (ReadResult page in textUrlFileResults)
        {
            foreach (Line line in page.Lines)
            {
                Console.WriteLine(line.Text);
            }
        }
    }
}  
```

**Python**

```Python
# Use Read API to read text in image
with open(image_file, mode="rb") as image_data:
    read_op = cv_client.read_in_stream(image_data, raw=True)

    # Get the async operation ID so we can check for the results
    operation_location = read_op.headers["Operation-Location"]
    operation_id = operation_location.split("/")[-1]

    # Wait for the asynchronous operation to complete
    while True:
        read_results = cv_client.get_read_result(operation_id)
        if read_results.status not in [OperationStatusCodes.running, OperationStatusCodes.not_started]:
            break
        time.sleep(1)

    # If the operation was successfuly, process the text line by line
    if read_results.status == OperationStatusCodes.succeeded:
        for page in read_results.analyze_result.read_results:
            for line in page.lines:
                print(line.text)
```
    
4. **GetTextRead** 함수에 추가한 코드를 살펴봅니다. 이 코드는 읽기 작업 요청을 제출한 다음 작업이 완료될 때까지 상태를 반복 확인합니다. 작업이 정상적으로 완료된 경우 코드는 각 페이지와 각 페이지 줄에서 차례로 반복 실행되어 결과를 처리합니다.
5. 변경 내용을 저장하고 **read-text** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

6. 메시지가 표시되면 **2** 를 입력하고 출력을 살펴봅니다. 출력은 문서에서 추출된 텍스트입니다.

## <a name="read-handwritten-text"></a>필기 텍스트 읽기

**Read** API는 인쇄된 텍스트뿐 아니라 영어 필기 텍스트도 추출할 수 있습니다.

1. 애플리케이션용 코드 파일의 **Main** 함수에서 사용자가 메뉴 옵션 **3** 을 선택하면 실행되는 코드를 살펴봅니다. 이 코드는 **GetTextRead** 함수를 호출하여 이미지 파일의 경로를 전달합니다.
2. **read-text/images** 폴더에서 **Note.jpg** 를 열어 코드가 처리할 이미지를 표시합니다.
3. **read-text** 폴더의 통합 터미널에서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

4. 메시지가 표시되면 **3** 을 입력하고 출력을 살펴봅니다. 출력은 문서에서 추출된 텍스트입니다.

## <a name="more-information"></a>추가 정보

**Computer Vision** 서비스를 사용하여 텍스트를 읽는 방법에 대한 자세한 내용은 [Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)를 참조하세요.
