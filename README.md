# Face-Recognition

얼굴 인식 레파지토리👧🏻

## 코드 보면서 알게 된 내용들

1️⃣ model 관련

- ### prototxt

  - 네트워크 구조가 담긴 파일

  - 이를 수정하여 네트워크 성능 개선 가능

  ![image](https://user-images.githubusercontent.com/66320010/162871082-70f37065-cca9-4c5c-aef0-ac6701a99a3c.png)

- ### caffemodel

  - prototxt의 네트워크로 학습시킨 모델

  - weight와 bias가 담겨져 있음

  - 일반적으로 볼 수 없는 파일이지만 caffe플랫폼으로는 열어서 수정 가능

  ![image](https://user-images.githubusercontent.com/66320010/162870095-33411309-2b44-4376-8499-90e4046e85d4.png)
  
  
2️⃣ torch 관련

- ### torch.nn.parameter

    - Tensor의 한 종류로, Module에 속성으로 할당될 때 자동으로 매개변수로 등록됨
    
    - Module에 역전파 동안 업데이트가 필요한 가중치가 있음을 알려주는 텐서용 래퍼

    - requires_grad속성이 설정된 텐서만 업데이트됨
