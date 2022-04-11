# SphereFace

- 얼굴 인식(face recognition)은 크게 face identification과 face verification 태스크로 나뉘어진다.

- **Face identification**이란 입력으로 들어온 얼굴을 특정한 사람으로 특징짓는 것을 말하며, **Face verification**은 두 개의 얼굴이 같은 사람인지 다른 사람인지 판단하는 문제이다.

- 얼굴 인식은 얼굴로 사람을 구별하는 분류 문제일텐데 각 사람(클래스) 별로 다양한 각도의 얼굴 이미지를 가지고 있을 때 얼굴 인식 테스트는 어떻게 수행할까?

- 분류에서는 테스트 시에 closed-set / open-set 프로토콜이 존재한다.

- closed-set은 테스트 데이터의 클래스가 훈련 데이터의 클래스에 포함되는 경우로 우리가 아는 일반적인 분류문제이다. 예를 들어 강아지/고양이를 분류하는 training set으로 학습했다면 test set도 강아지/고양이로만 되어있다.

- 반면, open-set은 테스트 데이터의 클래스가 훈련 데이터의 클래스에 포함되지 않는 경우로서 일반적인 분류문제처럼 softmax를 통한 판단이 무의미하다. 예를 들어 강아지/고양이로 학습했지만 test는 호랑이/사자를 분류하게 되는 경우이다. 즉, 특정 class의 feature를 학습한다기보다는 서로 다른 class의 feature끼리는 유사도가 낮고 같은 class의 feature는 유사도가 높도록 학습해야한다. 이런 학습을 metric learning이라고 한다.

![image](https://user-images.githubusercontent.com/66320010/162711691-b14e4000-0c9b-497f-a9fe-ba19468e9108.png)

- 얼굴 인식에서는 학습할 수 있는 얼굴은 제한되어 있지만 실제 응용하기 위해서는 학습에 사용되지 않은 새로운 얼굴도 구별할 수 있는 고유한 feature를 만들어야 하므로 Metric learning에 적합하다고 볼 수 있다.

- 하지만 일반적으로 얼굴 이미지는 다른사람이더라도 비슷한 경우가 있고 같은 사람이더라도 이미지가 찍힌 배경, 환경 등에 따라 매우 달라 이 목적을 달성하기 어렵다.

- CNN을 이용한 얼굴 인식은 SphereFace가 등장하기 전에는 cross-entropy를 통한 softmax 목적함수나 positive / negative 데이터 pair를 통해 훈련하는 contrasive / triplet loss등을 사용해왔다.

- 하지만 softmax함수는 클래스끼리 잘 구분할 뿐, 같은 클래스끼리는 뭉치고 다른 클래스끼리는 떨어지게 하는 discriminative한 동작은 잘 수행하지 못하며 contrasive / triplet 함수는 꼼꼼한 데이터 parir를 구축하는 것이 필수적이라 사용하기에 제한적이다.

- 따라서 SphereFace는 A-Softmax Loss라는 새로운 loss는 제안한다.

### A-Softmax Loss

