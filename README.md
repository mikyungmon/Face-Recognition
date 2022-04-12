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
  
  
2️⃣ 프레임워크 관련

  인공지능 연구 및 서비스 개발자의 요구사항, 지원언어 및 하드웨어에 따라 여러 종류의 딥러닝 프레임워크가 개발되어 공개되었음
  
  대표적인 것으로는 MxNet, Caffe, Theano, TensorFlow, Torch 등이 있음
  
 ![image](https://user-images.githubusercontent.com/66320010/162891298-dbf570dc-e40b-4765-8f79-e0d3772d6599.png) 

[pytorch]

- ### torch.nn.parameter

    - Tensor의 한 종류로, Module에 속성으로 할당될 때 자동으로 매개변수로 등록됨
    
    - Module에 역전파 동안 업데이트가 필요한 가중치가 있음을 알려주는 텐서용 래퍼

    - requires_grad속성이 설정된 텐서만 업데이트됨


[MxNet]

  - 아마존 CTO인 Werner Vogels 박사는 확장능력, 개발 속도, 이동성 등의 다양한 요인을 비추어볼 때 MXNet이 가장 좋은 딥러닝 프레임워크라고 판단하고 이를 기반으로 한 딥러닝 서비스 개발 지원 및 오픈 소스 지원에 대한 의지를 피력한 적 있음
  
  - MXNet의 주요 기능 중 하나는 CPU와 GPU에서 동일하게 실행될 수 있다는 것. 즉, 컴퓨터에 NVIDIA GPU (MacBook과 동일)가 없더라도 나중에 AWS에서 제공하는 GPU 기반 가상 클라우드 인스턴스에서 사용 가능한 MXNet 코드를 작성하고 실행 가능
  
  - 내 PC에 GPU가 있으면 CUDA 및 cuDNN툴킷을 설치해야함. 그런데, 이 도구를 설치하는 것이 초보자 입장에서 매우 까다롭고 MXNet 바이너리와 Nvidea 도구 사이의 비 호환성으로 인해 설정이 깨져서 작업하기가 어려울 수 있음

  - 이러한 이유로 MXNet 웹 사이트에서 제공되는 Docker 이미지 (CPU 환경 용, GPU 환경 용) (nvidia-docker가 필요함)를 사용하는 것이 좋음. 이 이미지에는 필요한 모든 것을 사전 설치하여, 몇 분 안에 시작 가능

  - 임포트는 다음과 같이 함

    ![image](https://user-images.githubusercontent.com/66320010/162891845-c693d389-60c8-451e-a0dd-41fa0bdb5421.png) 
