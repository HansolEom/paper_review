Abstract
심층 신경망은 훈련하기 더 어렵다.우리는 이전에 사용된 것보다 훨씬 더 깊은 네트워크를 쉽게 훈련하기 위해 residual learning framework를 제시한다. 참조되지 않은 함수를 학습하는 대신 레이어 입력을 참조하여 residual functions을 학습하는 것으로 레이어를 명시적으로 재구성합니다. 우리는 이런 residual networks가 최적화하기 쉽고 상당히 깊은 networks에서 정확도를 얻을 수 있음을 보여주는 포괄적인 경험적 증거를 제공한다. ImageNet 데이터셋에서 우리는 VGGnet보다 8배 깊은 152layer 깊이로 residual networks로 평가하지만 더 낮은 복잡도를 가진다. 이러한 residual networks의 앙상블은 ImageNet 테스트세트에서 3.57% error를 갖는다. 이 결과 ILSVRC2015 Classification task에서 1위를 했다. 또한, CIFAR-10에서 100, 10000 layers에 대한 분석을 제공한다. 
representations(feature가 담고있는 표현)???의 깊이는 많은 시각적 인식 작업에서 매우 중요합니다. 매우 깊은 표현 덕분에 COCO object detection dataset에서 28%의 상대적인 개선을 얻었다. deep residual Nets은 ILSVRC&COCO2015 대회 제출에 토대로 ImageNet detection, ImageNet Localization, COCO detection, COCO segmentation에서 1위를 차지했다. 

1. Introduction
	Deep convolutional neural networks은 Image Classification을 위한 획기적인 발전을 가져왔다. Deep networks는 자연스럽게 low/mid/high level feature와 end-to-end multi layer방식으로 통합하며, feature의 levels은 쌓이는 layers(depth) 수에 의해 강화 될 수 있다. 최근 흔적을 보면 networks의 깊이가 매우 중요하며 ImageNet datasets의 주요 결과는 16~30의 깊이를 사용하는 매우 깊은 모델을 활용한다. 많은 다른 적지않은 시각적 인식 작업에서 매우 깊은 모델의 이점을 많이 얻었다.
	깊이의 중요성에 따라 질문이 생긴다: 더 많은 layer를 쌓는 것이 더 쉬운 networks 학습인가? 이 질문에 대한 답을 하는데 장애가 된것은 수렴을 방해하는 gradient vanishing/exploding라는 악명높은 문제가 있다. 그러나 이 문제는 normalized initialization 및 중간 normalization layers에 의해 주로 해결되었다. 이 layer는 수십개의 계층을 가진 네트워크간 backpropagation을 통해 SGD를 위해 수렴을 시작할 수 있도록 한다. 
	deeper networks가 수렴을 시작할때 성능 저하 문제가 노출되었다. 네트워크 깊이가 증가함에 따라 정확도가 포화되고 급격히 저하된다. 예기치 않게 이러한 저하는 과적합으로 인한 것이 아니며, 적절하게 깊은 모델에 더 많은 레이어를 추가하면 더 높은 훈련 오류가 발생한다. 그림 1은 전형적인 예를 보여준다. 

-> 깊이가 깊어졌을때 test와 train의 정확도의 차이가 크로스 되지 않기 때문에
훈련의 정확도 저하는 모든 시스템이 비슷하게 최적화하기 쉬운것이 아니라는 것을 나타낸다. 더 얕은 아키텍처와 더 많은 레이어를 추가한 깊은 아키텍처를 고려해 보면, 더 깊은 모델에 대한 구성 별 솔루션이 있다. 추가된 레이어는 identity mapping이고 다른 레이어는학습된 얕은 모델에서 복사된다. 이 구성의 솔루션은 더 깊은 모델이 더 얕은 모델보다 더 높은 훈련 오류를 생성하지 않아야함을 나타낸다. 그러나 실험에 따르면 현재 사용중인 solvers(optimizer)는 구성된 솔루션보다 비슷하거나 더 나은 솔루션을 찾을 수 없다. 
	이 논문에서 우리는 deep residual learning framework을 도입하여 성능 저하 문제를 해결한다. 몇개의 stack layer가 직접적으로 매핑 되기를 바라는 대신 잔여 매핑에 맞도록 명시적으로 사용한다. 공식적으로 밑에 매핑을 H(x)로 표시하고, 누적된 비선형 레이어를 F(x): H(x) -x의 다른 매핑에 맞춘다.(각 레이어는 목적과 인풋의 차이). 오리지널 매핑은 F(x)+x로 다시 캐스팅된다. 참조되지 않은 매핑을 최적화 하는 것보다 잔차 매핑을 최적화 하는 것이 더 쉽다고 가정한다. 극단적으로 아이텐티티 매핑이 최적이라면 비선형 레이어 스택으로 아이덴티티 매핑을 맞추는 것보다 잔차를 0으로 밀어내는 것이 쉬울것이다. 

