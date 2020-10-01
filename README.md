# LapSRN : https://arxiv.org/abs/1704.03915

#### <I studied while referring to various sites, but it is not enough. Thanks anytime for feedback.>
### <heejo5@naver.com>

Deep Laplacian Pyramid Networks for Fast and Accurate Super-Resolution
--------------------------------------------------------------------------

* SRCNN, VDSR, DRCN 같은 네트워크는 Bicubic Upsampling된 LR img를 input으로 사용을 하였으며 이는 불필요한 계산량 증가를 일으켰고 또한 HR 복원 과정 중 추가적인 고 주파수를 사용할 수 없었
* 두 번째로 Loss Function으로 자주 사용하는 L2 Loss는 기존의 방법들은 L2 Loss를 이용하여 Network를 최적화 시켜왔지만 L2 Loss는 모호한 예측을 생성하였으며 재구성 된 HR이미지는 인간의 시각적 인식에 가깝지 못하였음
* 세 번째로 대부분의 방법들은 HR을 한번에 Upsample하는데 큰 Scale을 training할 때 어렵고 중간 단계의 SR을 확인 할 수 없다는 단점을 가지고 있음
* 따라서 LapSRN은 input으로 Upsampling 되지 않은 LR Img를 가지고 Convolution을 통해 점진적으로 Residual Img를 예측하고 Transposed Convolution을 통해 각 단계마다 특징맵을 Up sampling 시킴
* Laplacian Pyramid 개념을 SR에 접목시킨 LapSRN을 제안 


LapSRN Network
--------------
![image](https://user-images.githubusercontent.com/61686244/94776930-936dfc00-03fd-11eb-9339-7fd2271dce59.png)
* 기존의 네트워크들중 FSRCNN을 제외하고 SRNN, VDSR, DRCN등은 Network에 들어가기전에 bicubic과 같은 보간 방법으로 Upsampling을 하여 입력을 하였음
* Network에 들어가기전에 먼저 Upsampling을 하는 것은 불필요한 계산량 증가와, HR 복원중 고 주파수를 사용할 수 없고 FSRCNN과 같은 경우는 마지막에 한 번에 upsampling을 하게되면 큰 scale을 트레이닝 할때 어려움과 중간 단계의 SR을 확인 할 수 없다는 단점을 가지고 있음
![image](https://user-images.githubusercontent.com/61686244/94777158-f19adf00-03fd-11eb-93c3-536bfd06a84f.png)
* LapSRN의 구조를 보게 되면 LapSRN은 Feature Extraction과 Reconstruction 단계로 구성
* Red는 Convolution을 의미하고 Blue는 Transposed Convolution을 의미하고 Green은 Element Wise sum을 의미
* One step Upsampling 와는 다르게 제안된 네트워크는 점진적으로 HR의 Residual을 다수의 피라미드 레벨에서 복원
* 여기서 LV의 단계 S는 scale factor로 예를 들어 x8의 scale img를 원하면 총 LV단계는 1,2,3 단계로 이루어지게됨
* Input으로 들어온 LR에서 직접 특징을 추출함으로 낮은 계산량을 요구
* 각 레벨 마다 하나의 Residual img를 출력하게 되며, LapSRN의 Feature Extraction을 먼저 보게 되면 LV S에서는 d 개의 Convolution 와 하나의 transposed Convolution이 존재
* 각 transpose Conv의 출력은 다른 두개로 연결이 돼있는데 하나는 LV S에서 Residual img를 재 구성하기위한 Convolution layer로 들어가고 다른 하나는 Lv S +1 단계로 다시 특징을 추출하기위한 Convolutional Layer로 들어가게됨
* 낮은 LV S의 특징들은 높은 레벨 S와 공유 됨으로 복잡한 맵핑을 학습하기위한 네트워크의 비선형 맵핑을 증가 시킬 수 있다고함
* 두번째 단계로 Image Reconstruction단계로 LV S에서 input은 transposed convolutional layer에 의해 upsampling된 img이고 upsampled된 img는 Feature Extraction에서 추출한 Residual img와 element wise sum을 통해 합쳐지게됨
* 이렇게 합쳐진 LV S에서의 HR output img는 다음 img reconstruction 단계인 LV S+1로 입력이 들어가게됨

Loss Function
-------------
![image](https://user-images.githubusercontent.com/61686244/94777778-fdd36c00-03fe-11eb-8fcc-7e8cb7064f22.png)

Conclusions
-----------
![image](https://user-images.githubusercontent.com/61686244/94777830-16438680-03ff-11eb-95b1-758dd4ede527.png)
* 제안된 네트워크를 이용하여 Residual learning의 유무 다른 Loss function에 따른 결과, Pyramid structure의 유무, Network 깊이에 따라서 4가지 실험
* 첫 번째 Residual learning의 유무 결과로 residual 학습의 효과를 설명하기위해 residual 을 제거한 것과 제거하지 않은 네트워크의 결과를 비교
* Residual을 사용하지 않은 네트워크는 더 느리고 크게 변동을 하는 것을 확인 할 수 있었으며 반면에 Residual을 사용한 LapSRN은 10번의 epoch만에 SRCNN의 성능에 도달 
* L2 Loss 와 Robust Loss를 사용해서 비교한 결과로 L2 Loss를 사용한 초록색 결과는 SRCNN과 비교했을때  LapSRN 보다 더많은 이터레이션을 요구한걸 알 수 있고 반면에 LapSRN은 부드럽게 곡선이 올라가는 것을 확인 할 수 있음
* Pyramid Structure의 유무에 따른 결과로 FSRCNN의 네트워크와 비슷하지만 Residual 학습 방법을 사용
* Pyramid구조를 사용하여 점진적으로 네트워크를 키워나가 원하는 출력을 얻은 LapSRN인 두개의 테스트 데이터셋 대비 0.7, 0.4더 높았고 Figure3을 보더라도 더 좋은 결과를 내보내는것을 확인 할 수 있음
* 제안된 네트워크에 사용되는 네트워크의 깊이에 따른 결과로 Table 3와 같은 결과를 얻어 냈음
*  일반적으로 더 깊은 네트워크가 더 좋은 결과를 만들어 냈지만 x2, x4 SR모델에서는 속도대비 PSNR에 따라서 깊이를 10으로, x8 SR모델에서는 깊이를 5로 사용하는게 가장 좋은 결과를 낼 수 있다고함
![image](https://user-images.githubusercontent.com/61686244/94777859-23607580-03ff-11eb-9c40-3bec30b66331.png)
![image](https://user-images.githubusercontent.com/61686244/94777875-2a878380-03ff-11eb-825d-6aee4c9e33cb.png)
![image](https://user-images.githubusercontent.com/61686244/94777890-2fe4ce00-03ff-11eb-972f-7ffd8bc0dd79.png)
![image](https://user-images.githubusercontent.com/61686244/94777904-36734580-03ff-11eb-943d-5166e8daf45d.png)

