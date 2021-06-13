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

## Scaling

### scaling의 의미
- x1(연봉처럼 단위가 큰 데이터), x2(나이처럼 단위가 작은 데이터)의 가중치 w1, w2가 있다면 y=x1w1 + x2w2 + ... 이런식이기 때문에 w1과 w2간의 불균형이 이뤄지고
- loss function의 gradient descent 또한 w2를 줄이는것에 치우쳐지기 시작하므로 scaling을 꼭 해줘야함
- 
### Batch Normalization
- 신경망 내부에서 층 을 통과할때마다 입력 데이터 분포가 변함 (Internal covariate shift) (귀마개 끼고 단어 전달하기 게임 생각하면 이해가 쉬움)
- 정규화 후에 scaling, shift로 너무 loss함수에 충실하지 않게 일종의 noise 추가해주어 좀더 일반화된 모델이 되는 효과
- weight initialization을 크게 신경쓸 필요가 없어짐
- activation 전에 batch normalization 적용

### Shuffle
- 일반적으로 shuffle하면 성능이 좋아짐
- batch 안에서의 shuffle과 전체 데이터의 shuffle을 잘 구분해야함 

## 성능 향상을 위해 알아야 할 것

### batch size
- 대부분의 경우 batch size는 작을수록 성능이 높아짐
- 8보다는 크고 32보다는 작은 수일때 가장 높은 성능

### learning rate
- ```ReduceLROnPlateau```와 같은 learning rate 동적변경을 해줘야 성능 향상에 큰 도움이 된다

### Callback
- 몇 iteration동안 성능 향상이 없다면 early stopping을 통해 일찍 중단시키거나 
- monitor하고 싶은 (loss 함수라던지, accuracy라던지, val_loss 라던지) 부분의 성능이 가장 좋을때의 weight값을 checkpoint로 저장시킬수도 있음

### Filter, Layer 수
- filter수나 layer 수가 많아질수록 성능이 조금 좋아지기는 하지만 모든 경우에 이런 것은 아니다
- filter수와 layer 수가 많아질수록 parameter갯수가 많아지기 때문에 마지막 단계에서 ```MaxPooling2D```를 사용하지 않고 strides=2로 줘 maxpooling과 비슷한 효과를 내며 연산 속도를 줄일 수 있다

### Global Average Pooling
- 가로세로 영역을 subsampling 하는 것이 아닌, 채널별로 평균 값을 구하는 방법
- Feature map의 채널수가 많을 때 사용, 채널수가 많이 없으면 (512 이하정도) Flatten이 유리
 - Flatten에서 Classification dense layer로 넘어갈 때 파라미터가 너무 많아 오버피팅이 될 가능성이 높기 때문

### Weight Regularization
- 모델이 loss를 줄이는데만 집중하여 오버피팅 되는 것을 막기 위해 가중치에도 규제를 줌
- loss 함수에 작은 수를 더해줘서 loss 결과가 어느정도 무뎌지게 만듦
- 큰 성능개선은 기대하기 힘듦

### Gradient tape
- 
