# DeepLearning

### loss 함수
- categorical crossentropy
  - 분류해야할 클래스가 3개 이상인 멀티 클래스일 경우 사용
- sparse categorical crossentropy
  - categorical crossentropy와 같으나 타겟 값이 0,1,2,3 처럼 정수 형태로 제공될 때 

### activation 함수
- 은닉층에 ReLu를 사용하는 이유
  - sigmoid와 다르게 미분했을때 0이 되지않아서 vanishing gradient 문제를 해결함
- 마지막 classification 함수 기준
  - 이진분류일 경우 sigmoid
  - 멀티클래스일 경우 softmax

### Dense layer
- Dense layer는 입력과 출력을 이어주는 은닉층으로 주로 사용
- Dense layer에서 activation='relu'라고 설정해주는 것의 의미
  - 이미지로부터 추출한 feature map에서 양의 값만 활성화 시키는 것
  - 이미지에서 특히 두드러지는 특징을 추출하는 것 

### CNN 개요
- Convolution + Classifier로 구성
- Classifier로는 Dense layer, dropout layer가 포함된다

### kernel과 filter 차이
- kernel 이 여러개 모여서 filter를 이루는것
- filter 하나에 kernel이 몇개있는지 아는 법 --> Input shape의 차원수와 동일함 (28, 28, 1) 이면 filter당 kernel 도 1개

### Convolution 연산 시 kernel, filter 수에 대한 이해
- Input으로 (3,3,2)가 들어오게되면 한개의 filter의 kernel(channel)수는 2이다
  - convolution 연산을 위해 아구가 맞아야 하기 때문
  - 출력 feature map의 channel 수는 filter 수와 동일
#### 정리하자면 단일 filter의 channel 수는 입력 feature map의 channel 수와 동일하고 전체 filter의 갯수는 출력 feature map의 channel 수와 동일하다

### output shape 구하는 공식
- O = (I - F + 2P)/S + 1
  - O : output, I : Input, F : kernel size, P : padding, S : stride
  - input이 5x5이고 output은 3x3으로 만들고 kernel size가 3x3이고 stride를 2칸씩 가고싶다면 padding을 얼만큼 늘려야할까?
  - 3 = (5 - 3 + 2P)/2 + 1 ==> P = 1

### kernel size 정하는 기준
- 이미지의 크기가 작은경우 kernel size를 크게하면 데이터 손실이 많이 일어날 수 있기 때문에 작은 kernel size로 해도 충분하다

## Weight Initialization method

### Xavier glorot initialization
- 초기 weight를 설정할 때 효율적으로 초기화하도록 하는 방법
- 입력노드(fan in)와 출력노드(fan out)이 많아질수록 표준편차가 작아짐 (가파른모양)
- 따라서 glorot normal을 이용하여 적당히 조절해줌
- 주로 sigmoid나 hyperbolic tangent h 에 최적화된 방법
- keras 기본 kernel_initializer로 glorot_uniform이 설정돼있음

### He initialization
- Relu에 보다 최적화된 초기화방법
- xavier 방법이랑 비슷한데 fan in으로만 계산함
  
