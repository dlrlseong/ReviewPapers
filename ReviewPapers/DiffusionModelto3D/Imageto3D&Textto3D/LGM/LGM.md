# LGM: Large Multi-View Gaussian Model for High-Resolution 3D content Creation
---
읽기전 생각 : DreamGaussian의 저자가 쓴 새로운 논문.. ICLR에 이어 ECCV Oral까지.. 메이저 컨퍼런스에 Oral로 연속 두번 하시는 대단하신 분이다... DreamGaussian을 넘어 성능도 뛰어난 것으로 보이는데 이 논문의 후속 논문도 꽤 벌써 많이 나온듯하다. 빨리 읽어보고 코드도 돌려봐야 겠다...!

---
# Abstract
초록에서 일단 `current feed-forward models`라고 했는데, 이 부분은 일단 뒤에서 찾아봐야겠다. 주요 insights에서 두가지를 언급하는데 첫번째는 새로운 3D 표현 방법인데, `which can then be fused together for differentiable redering`이라고 하는데 아직 뜻이 와닿지는 않는다. 이 의미도 논문을 읽으며 찾고 이해해보려 한다. 두번째는 3D Backbone인데, `asymmetric U-Net`을 선보인다고 하는데, 이게 어떤것인지 어떤 역할을 하는지 찾으며 읽어야 겠다. 그리고 놀라운 점은 Image-to-3D 나 Text-to-3D 작업을 5초만에 한다고 한다...
Keywords: 3D generation, Gaussian Splatting, High Resolution

---
# Introduction
- 3D Content 생성은 다양한 분야에서 큰 잠재력을 가지고 전문가의 노력없이 많은 노력을 줄여준다.
- 이전에는 SDS(Score distillation sampling) 방식으로 2D diffusion을 3D로 lift하는 방식의 최적화방식의 방법론을 사용했다. 그러나 이들은 느린 생성 속도와 다양성의 한계에 종종 마주친다. ~~아마 DreamFusion이 베이스 논문일거다.~~
- 최근에는 트랜스포머와 `Triplane-based NeRF`를 사용하는 `Large Reconstruction Model(LRM)`들이 등장한다. 이러면 시간이 줄어들지만, 낮은 해상도의 훈련으로 인해, 디테일한 텍스처와 정교한 기하학적 형태를 만들지 못한다.
- `triplane-based volume rendering` 또는 트랜스포머들 사용 없이 독창적인 few-shot 3D 재구성 모델을 선보임. (여기서 triplane-based라고 하면 triplane-NeRF인가 그거인듯함)
- 그것은 `asymmetric U-Net`백본에 의해 예측된 특징의 3D Gaussian Splatting을 적용하는 것이다. (?) ㅋㅋ

- The motivation of this design is to achieve high-resolution 3D generation, which necessitates an expressive 3D representation and the ability to train at high resolutions. Gaussian splatting stands out for 1) the expressiveness of compactly representing a scene compared with a single triplane, and 2) rendering efficiency compared with heavy volume rendering, which facilitates high resolution training. However, it requires a sufficient number of 3D Gaussians to accurately represent detailed 3D information.

여기까지 읽어보면 일단 Triplane-NeRF 논문들의 한계점을 지적하고, 이를 개선하기 위해서 3D Gaussian Splatting을 적용했다?? 정도로 정리할 수 있겠음.

- Splatter Image[46]에서 영감을 받아, U-Net이 충분한 수의 가우시안들을 생성하는데에 효과적이라는 것을 알게됨. 이것은 고해상도의 훈련을 가능하게함.
- 우리의 방법은 Instant3D [19]와 유사한 다중 뷰 재구성 설정을 채택합니다. 이 과정에서 각 입력 뷰에서 이미지와 카메라 임베딩이 특징 맵으로 변환되고, 이는 가우시안 세트로 디코딩 및 결합(fuse)될 수 있습니다. 결합된 3D 가우시안에서 새로운 뷰를 렌더링하기 위해 미분 가능 렌더링이 적용되며, 이를 통해 고해상도에서의 엔드투엔드 이미지 수준 감독이 가능해집니다. 모든 입력 뷰에서 정보 공유를 강화하기 위해 U-Net의 깊은 층에 어텐션 블록이 통합됩니다. 이는 다중 뷰 이미지 데이터셋 [12]에서 회귀 목표만을 사용하여 네트워크를 훈련할 수 있게 합니다. 추론 중에 우리의 방법은 다중 뷰 이미지 확산 모델 [27,43,44,51]을 활용하여 기존 이미지 또는 텍스트를 입력으로 사용하여 다중 뷰 이미지를 생성합니다. 실제 3D 객체에서 렌더링된 다중 뷰 이미지와 확산 모델을 사용하여 합성된 이미지 간의 도메인 격차를 극복하기 위해, 우리는 견고한 훈련을 위한 두 가지 적절한 데이터 증강 기법을 제안합니다. 마지막으로, 다운스트림 작업에서 폴리곤 메시가 선호되는 점을 고려하여, 생성된 3D 가우시안을 부드럽고 텍스처가 있는 메시로 변환하는 일반적인 알고리즘을 설계했습니다. (24.08.29)