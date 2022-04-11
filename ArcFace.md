# ArcFace

ArcFace를 이해하기 전 필요한 개념들을 먼저 정리해본다.

1) open-set face recognition vs. closed-set face recognition

    - ArcFace논문에서는 기존 방법론의 한계점을 지적하는 과정에서 closed-set에 대한 언급을 하게 되는데 이는 선행 연구 중 하나인 SphereFace에서 언급되었다.

    - closed-set face recognition은 내가 맞춰야하는 test set에 있는 얼굴 사진에 대한 label이 training set에 있는 경우를 말하며 classification 문제와 동일하다.

    - 하지만 현실에서는 우리가 한번도 본적 없는 얼굴에 대해서 인식을 해야하는 일이 발생한다. 이에 해당하는 것이 바로 open-set face recognition이다.

    - open-set face recognition은 내가 맞추어야 하는 test set에 있는 얼굴 사진에 대한 label이 training set에 없는 경우를 말한다.

    - open-set의 경우 학습 데이터에 있는 label로 분류하는 것이 불가능하기 때문에 얼굴 이미지를 discriminative한 feature space로 mapping시키는 것이 요구된다.


2) Logit

    - neural network에서 classification task를 위해 사용되는 마지막 layer를 의미하고 실수 형태로 raw prediction value를 뽑아낸 결과를 의미한다.

    - 예를 들어 이미지를 주고 강아지, 고양이, 사자, 호랑이를 구별하는 classification을 해야한다면 cnn을 통해 특징을 추출한 후 마지막에 fully connected layer를 이용하여 최종적으로 4개의 클래스 중에 어떤 클래스에 가까운지에 대한 예측값을 뽑게된다.

    - 보통 분류 문제의 경우는 마지막에 softmax를 취해서 각 클래스에 해당할 확률의 합이 1이 되게끔 하는 처리를 해주지만 softmax를 취하기 전에 나온 raw prediction value값을 logit이라고 부르는 것 같다.


3) Geodesic distance

    - geodesic distance란 두 점을 잇는 최단 거리를 말한다.

### Abstract

- 대규모 얼굴 인식에서 DCNN을 사용하는 feature학습의 중요한 과제 중 하나는 분별력(discriminative power)을 향상시키는 적절한 loss함수 설계이다.

- Centre loss는 intra-class compactness을 달성하기 위해 유클리드 공간에서 deep feature과 이 feature에 해당하는 클래스 중심 사이의 거리에 패널티를 부과한다. 

- SphereFace는 마지막 fully connected layer에서의 linear transformation matrix(선형 변환 행렬)가 angular space에서 class의 중심을 나타내는 표현으로 사용될 수 있으며 deep features와 그것에 대응하는 weight사이의 각도에 multiplicative한 방법을 이용해 패널티를 부과한다.

- 최근 인기있는 연구분야는 face class separability를 최대화하기 위해서 잘 설계된 손실 함수에 margin을 포함시키는 것이다. 

- 이번 연구에서 discriminative한 feature를 얻기 위해 additive angular margin loss(ArcFace)를 제안한다. 제안된 ArcFace는 hypersphere에서의 geodesic distance와 일치하기 때문에 명확한 기하학적 해석을 가지고 있다.


### Introduction

- DCNN embedding을 사용하는 face representation은 얼굴 인식을 위한 최고의 방법이다. DCNN은 일반적으로 포즈 정규화 단계를 거친 뒤 얼굴 이미지를 클래스 내에서는 작은 거리를 가지고 클래스 간에는 큰 거리를 갖는 특징으로 mapping한다.

- 얼굴 인식을 위해서 DCNN을 학습시키는 두개의 주요한 연구분야가 있다. 

  1. softmax classifier를 사용하여 학습데이터의 다른 label(identity)를 구별할 수 있는 multi-class classifier를 학습하는 것
  2. triplet loss와 같이 embedding을 직접적으로 학습하는 것

- 대규모의 학습 데이터와 정교한 DCNN구조에 기반하여 softmax loss기반의 방법과 triplet loss기반의 방법은 얼굴 인식에서 훌륭한 성능을 얻을 수 있었지만 둘 다 결점을 가지공 ㅣㅆ었다.

- softmax loss 경우
  
  1. 선형 변환 행렬의 크기가 label(identity) 개수 n에 의해 선형적으로 증가한다.
  2.  closed-set 분류 문제에 대해서는 학습된 특징들이 분리될 수 있지만 open-set 얼굴 인식 문제에 대해서는 충분히 discriminative하지 않다.

- triplet loss 경우

  1. 대규모 데이터셋에서 face triplet의 수에서 combinatorial explosion(결합적인 폭발)이 존재할 수 있으며 이는 iteration stop(반복 단계)의 수에서 상당한 증가를 가져올 수 있다.
  2. semi-hard sample mining이 효과적인 모델 훈련에 대해 꽤 어려운 문제이다.


![image](https://user-images.githubusercontent.com/66320010/162738357-212e0981-da8c-4491-ba98-c3fddd84e631.png)

(a) margin-loss : sample과 중심 간의 geodesic distance margin을 추가(위 그림을 보면 알겠지만, sample과 중심 간의 거리 + m 이 sample과 다른 identity의 중심 간의 거리보다 작게끔 만드려고 한다. 따라서, 여기서의 margin이라는 의미는 sample과 다른 identity의 중심 간의 거리가 m이라는 값보다 크게끔 한다는 것이다. 만약 m이라는 값보다 작으면 penalty를 부과한다.)

(b) intra-loss : sample과 중심간의 geodesic distance를 감소시킨다.

(c) inter-losss : 다른 중심간의 geodesic distance를 증가시킨다

(d) triplet loss : triplet sample간의 geodesic distnace margin을 추가함

해당 논문에서는 Additive Angular Margin Loss(ArcFace)를 제안하며 이는 (a)에서의 geodesic distance margin penalty와 정확히 대응되며 얼굴 인식 모델의 discriminative power를 강화시킨다.








