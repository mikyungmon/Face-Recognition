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

기존의 softmax 계산식은 다음과 같다.

![image](https://user-images.githubusercontent.com/66320010/162715287-ffc1c606-b8a5-4bda-aaf2-cac1324d02ef.png)

x는 모델의 학습된 출력 feature이다. W_i,b_i는 마지막 Fully Connected Layer의 weight와 bias이다.

binary classification의 경우를 가졍하여 p1,p2만 있다고 가정해보자. p1 > p2인 경우는 class1로 예측할 것이고 반대인 경우에는 class2로 예측하게 된다.

이 때, 두개의 class를 결정하는 decision boundary(구분짓는 경계)는 (W_1 - W_2)x + b_1 - b_2 = 0가 된다.

위의 식에서 ∥W∥=1이 되도록 normalize해주고 b_i=0으로 두면 W_i^Tx=∥W_i^T∥ ∥x∥cos(θ_i) 이므로 (θ_i는 W_i와 x와의 각도) decision boundary를 아래식으로 다시 표현 가능하다.

cos(θ1)∥x∥ - cos(θ2)∥x∥ = 0      ( p1 = cos(θ1)∥x∥, p2 = cos(θ2)∥x∥ / 이렇게 표현한다면 decision boundary가 왼쪽 식과 같이 되며 이를 modified softmax loss라 칭하고 class i에 속하는 feature는 W_i와 작은 각도를 가지도록 훈련된다.)

feature x는 같으므로 실제로 두 개의 class를 구분하는 것은 θ1과 θ2라고 할 수 있다.

이에따라 softmax loss 식을 아래와 같이 다시 표현한 것을 modified softmax라고 한다.

![image](https://user-images.githubusercontent.com/66320010/162722967-9f338671-9dd9-4d5d-9e7e-63f124417c3f.png)

여기서 A-Softmax는 두 개의 θ1과 θ2가 더 잘 구분될 수 있도록 angle관점에서 margin을준다.

즉, 기존 cos(θ1) > cos(θ2)에서 cos(mθ1) > cos(θ2) 또는 cos(θ1) > cos(mθ2)과 같은식으로 margin값인 m을 준다.

그럼 위의 softmax 식은 아래와 같이 다시 표현할 수 있다.

![image](https://user-images.githubusercontent.com/66320010/162723985-75fb17af-cbf5-4055-ae16-ccb2e7681ca4.png)

지금까지 나온 loss에 대해 다시 살펴보면 아래와 같은 decision boundary를 갖게된다.

![image](https://user-images.githubusercontent.com/66320010/162724058-dc1b9da5-d110-429d-8063-4bdb946f1165.png)

이에따른 feature들의 분포를 보면 아래 그림과 같이 된다. original softmax를 쓰게되면 margin이 없기 때문에 decision boundary(angular bisector)가 정확하게 두 개의 클래스를 분류하지 못한다.

![image](https://user-images.githubusercontent.com/66320010/162725199-4cf5fa6f-3271-4d10-b6d5-767d29fe7aea.png)

(a)(b) 두번째로 margin이 없는 modified softmax loss를 보면 각도를 기준으로 분류하기 때문에 나름 잘 분류가 된다.

(c)(d) 여기서 margin을 준 A-softmax loss를 보면 두 개의 W1,W2가 각도를 기준으로 서로 멀리 떨어져 있기 때문에 discriminative한 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/66320010/162725499-dc743ad9-b12b-4245-9320-b4097cf6f736.png)

위 그림은 각 loss에 따른 feature의 분포를 2/3차원 hypersphere상에 표현한 것이다. 

triplet loss와 같은 Euclidean margin loss는 일반적인 Euclidean 공간에서 feature를 구분하나 modified softmax /  A-softmax loss는 ∥W∥를 1로 둠으로써 feature를 hypersphere상에 자연스럽게 매칭할 수 있음을 확인할 수 있다.

특히, A-softmax loss부분을 보면 x와 W1,W2의 hypersphere상의 거리가 w1,w2로 표현되어 있는데 angular margin m으로 인해 class1의 feature x가 mx1 < w2가 되도록 강제됨으로써 intra-class feature끼리 더욱 뭉치게 되도록 동작하는 것을 볼 수 있다.


∴ 정리하면 sphereface는 modified softmax loss가 각도로 feature를 구분하는 것에 착안해 각도 측면에서 feature를 구분하는 것을 어렵게 하기 위한 angular margin개념을 도입한다.

   위와 같이 간단한 이진 분류를 생각해보았을 때, modified softmax loss에서는 class1의 feature x가 잘 분류되도록 하기 위해 cos(θ1) > cos(θ2)가 필요하다.
    
  그렇다면 m >= 2인 경우에 cos(mθ1) > cos(θ2)를 만족하기 위한 θ1은 modified softmax loss경우와 어떻게 달라질까?
    
  m >= 2인 m이 곱해지다보니 cos(mθ1) > cos(θ2)를 만족하기 위해서 θ1이 modified softmax loss경우보다 더 엄격히 작아져야한다.
    
  마찬가지로 class2에 속하는 feature x에 대해서도 cos(θ1) > cos(mθ2)가 적용되므로 θ2가 더 작아져야한다.
  
  
  ### Discusion
  
  처음에 얼굴 인식을 하기 위해서는 같은 사람의 얼굴(inter-class variance)은 같이 분포해야하고 다른사람의 얼굴(intra-class variance)끼리는 멀리 분포해야한다는 목표가 있었다.
  
  기존 softmax loss를 통한 분류를 feature가 내재적인 angular distribution을 가지도록 훈련되어 angular boundary를 통해 클래스를 구분하기가 쉽지 않았다.
  
  SphereFace에서 제안하는 A-Softmax Loss는 이에 적합한 방법론으로 angular boundary를 유도하고 얼굴 인식을 위한 discriminative feature learning을 위해 angular margin m을 추가했다. 
  
  이 방법은 triplet / contrastive loss와 함께 훈련하기 위해 positive / negative데이터 pair가 필요하지 않기 때문에 쉽게 사용할 수 있다.
  
  하지만 이후에 margin을 줄 수 있는 더 좋은 방법론들이 제시되면서 A-Softmax Loss의 한계가 있음을 지적한다.

