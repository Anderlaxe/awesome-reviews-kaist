---
Inkyu Shin / LabOR: Labeling Only if Required for Domain Adaptive Semantic Segmentation / ICCV 2021
---

# LabOR \[Kor\]

[English version](https://awesome-davian.gitbook.io/awesome-reviews/paper-review/2022-spring-paper-review/iccv-2021-labor-eng) of this article is available.



##  1. Problem definition (문제 정의)

- Domain Adaptation (DA)

  - Domain adaptation 은 컴퓨터 비전 분야의 중요한 한 분야입니다.

  - Domain adpatation의 핵심 목표는, source domain을 가지고 학습한 신경망 모델이 target dataset에서도 좋은 성능이 나오도록 만드는 것 입니다. 일반적으로 Target dataset은 source와 많이 다른 스타일을 가지는 데이터셋 이기에, source에서 학습한 신경망이 target에서는 낮은 성능(심각한 성능 하락)을 보여주기도 합니다. 이러한 문제점을 해결하려고 하는 것이 DA의 핵심 목표입니다.

  - <img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img1.png?raw=true" alt="drawing" width="900"/>

    

- 비지도 Domain Adaptation (UDA)  

  - label 정보를 모두 알고 있는 source dataset을 가지고 학습시킨 신경망 모델이 Target 도메인에서도 잘 동작하게 만드는 것을 목적으로 합니다. 이때 Target dataset은  label 정보를 가지고 있지 않습니다. 따라서 비지도학습 기법을 사용해서 모델을 추가 학습해야합니다.
  - UDA에 대한 많은 연구가 진행됐음에도 불구하고, 지도 학습으로 학습된 모델보다 현저히 낮은 성능을 보여줍니다.

- 적은 target label을 가지고 Domain Adaptation. 

  - 위와 같은 UDA의 약점 때문에, 몇 연구자들은 target dataset의 label 정보를 아주 조금만 사용하는 것을 고려하기 시작했습니다.
  - 아주 적은 label 정보를 모으는 것은 많은 자원과 비용을 필요로 하지 않는다는 생각이 반영된 연구입니다.

- Semantic segmentation

  - 이미지 안에서 객체를 경계까지 정확하게 구분하는 과제를 말합니다. 픽셀 단위로 라벨링을 모두 수행합니다. 

  - <img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img2.png?raw=true" alt="drawing" width="900"/>

    

  

## 2. Motivation (연구 동기)

- target label 정보를 최소한으로 사용해서 (주석자(라벨링 작업을 하는 사람)이 최소한의 노력과 시간만 투자해서) 모델의 성능을 최대한으로 끌어낼 방법을 고민합니다. 
- 이미지의 어떤 픽셀에 대한 라벨 정보를 주어야, 이미지 분할 모델이 최고의 성능으로 학습될 수 있을까? 라는 모티베이션을 가지고 연구된 논문입니다. 
- 즉 이 논문은 라벨링이 필요한 포인트를 찾기. 를 주요 과제로 삼습니다. 다시 말해 효율적인 픽셀 레벨 샘플링 작업이라고 표현할 수도 있습니다. 

### Related work (관련 논문)

1. 비지도 Domain Adaptation
   - Adversarial learning (적대학습) 은 source와 target을 모델을 사용해 추론했을 때 나오는 결과가, 피처의 분포 차이가 최소한으로 나오는것을 목표로 합니다.
   - 대표적인 논문으로는 \[[AdaptSeg](https://arxiv.org/abs/1802.10349), [ADVENT](https://arxiv.org/abs/1811.12833)\] 이 있습니다.
   - 많은 비지도 DA 연구가 진행되어 왔음에도 불구하고, 이 기법을 사용해 개발된 모델과 지도 학습을 사용해 개발된 모델의 성능차이가 아직까지도 극명하게 나고 있습니다.
2. 적은 target label을 가지고 Domain Adaptation. 
   - 위와 같은 비지도 학습의 문제점을 해결하고자, 많은 연구자들은 자원이 너무 많이 필요하지 않는 선에서, 아주 조금의 라벨링 데이터를 사용하는 것을 시도했습니다. 대표적인 논문으로는 \[[Alleviating semantic-level shift](https://arxiv.org/abs/2004.00794), [Active Adversarial Domain Adaptation](https://arxiv.org/abs/1904.07848), [Playing for Data](https://arxiv.org/abs/1608.02192), [DA_weak_labels](https://arxiv.org/abs/2007.15176)\] 이와 같은 것 들이 있습니다.
   - 이 논문들은 보통 이미지 단위 고려를 합니다. 즉 "어떤 이미지를 라벨링 하는게, 모델 성능을 최대로 만들까?" 라는 고민을 합니다. 
   - 반대로, 이 논문은 "어떤 픽셀을 라벨링 하는게, 모델 성능을 최대로 만들까" 라는 고민을 합니다. 

### Idea (아이디어)

- 이 논문은 새로운 예측 모델을 추가로 두고, 이것을 "불확실 영역"을 찾기 위해서 사용합니다. 이 "불확실 영역"만을 주석자가 라벨링한다면 적은 자원으로도 높은 성능을 가지는 모델을 만들 수 있을거라는 아이디어를 가지고 있습니다.
- 이 "불확실 영역" 다르게 포현하면, 최고의 성능 잠재력을 가지고 있는 지점, 영역. 이라고 해석될 수 있습니다.





## 3. Method (방법론)

### 3.1 Method Summary (방법론 요약 정리)

- 아래의 순서대로 기제된 방법과 이미지를 함께 보시길 바랍니다.
- 아래의 설명 순서는 이미지 초록색 번호와 동일합니다.

1. 픽셀 선택 모델 (**pixel selector model**)은 공유되는 하나의 backbone모델과 2개의 classifiers 모델로 구성됩니다.
2. 하나의 target 미니배치 이미지들은 위 backbone과 2개의 classifier를 통과하여, 예측결과가 추론됩니다. 2개의 classifier를 사용해서 나오는 "2개의 이미지 분할 예측 결과"가 나온다고 할 수 있습니다.
3. "2개의 이미지 분할 예측 결과"는 **불일치성 마스크**(Inconsistent Mask = 예측결과가 서로 다른 영역) 을 찾기 위해 사용됩니다.
4. 위에서 찾은 **불일치성 마스크** 를 참고해서 주석자는 target 적은 라벨링을 수행합니다. 이러한 라벨링은 이미지 분할 모델(semantic segmentation model)을 학습하기 위해 사용됩니다.
4. 위에서 얻은 target 적은 라벨과 원래 가지고 있던 source 라벨은 이미지 분할 모델을 지도 학습으로 학습하기 위해 사용됩니다. 동시에 적대적 학습법(adversarial learning \[[AdaptSeg](https://arxiv.org/abs/1802.10349)\])이 어떤 domain 이미지가 들어오든 비슷한 피처 분포를 가지게 하기 위해서 사용됩니다.
6. 마지막으로, 픽셀 선택 모델을 학습하기 위해서 최대 차이 기법\[[Maximum classifier discrepancy](https://arxiv.org/abs/1712.02560)\] 이 사용됩니다. 이 기법은 두 classiifer안의 파라미터들이 서로를 밀며, 서로 멀어지게 유도하는 기법입니다.

<img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img3.png?raw=true" alt="drawing" width="900"/>



### 3.2 Details of methods (방법론 세부사항)

- Loss1,2: 원래 가지고 있던 source label과 주석자의 라벨링으로 만들어진 적은 target label을 가지고 Cross entropy loss가 UDA model(이미지 분할 모델)에 적용되어 학습됩니다.
- Loss3: 적대적 학습법입니다. 자세한 사항은 다음 논문을 참고하면 됩니다. [AdaptSeg](https://arxiv.org/abs/1802.10349)
- Equ 4:  **Inconsistent Mask** 불일치성 마스크를 찾기 위한 공식입니다. 
- Loss5: 픽셀 선택 모델을 학습하기 위한 Pseudo(가짜) label loss입니다. Pseudo label(가짜 라벨)은 이미지 분할 모델이 예측한 결과를 그대로 사용합니다. 더 자세한 방법으로 해당 논문([IAST](https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123710409.pdf))의 방법을 그대로 사용했습니다.
- Loss6: 최대 차이 기법 로스 함수 입니다. (The classifier discrepancy maximization) (자세한 사항은 이 논문 방법을 그대로 사용했습니다. [MCDDA](https://openaccess.thecvf.com/content_cvpr_2018/papers/Saito_Maximum_Classifier_Discrepancy_CVPR_2018_paper.pdf) paper)

<img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img4.png?raw=true" alt="drawing" width="900"/>



### 3.3 Segment-based (영역 기반 기법) and Pinted-based(포인트 기반 기법)

- 이 논문이 라벨링 영역을 찾는 방법은 2가지로 나눠집니다. 하나는 영역 기반기법 “Segment based Pixel-Labeling (SPL)” 이고, 다른 하나는 포인트 기반 기법“Point based Pixel-Labeling (PPL).” 입니다.
- **SPL** 은 두 classifier를 사용해 추론된 예측 결과의 차이 (위 the inconsistency mask 참조) 를 그대로 사용한 영역입니다.
- 위 기법은 아래의 그림처럼, 굉장히 라벨링이 힘든 영역을 이기도 합니다. 더 좋은 효율성을 위해 **PPL** 기법은 위 영역 중 20~40개의 포인트만을 골라 사용하는 방법입니다. 아래는 이 포인트를 찾는 과정을 설명합니다. 
  1. **the set of uncertain pixels** D^(k) 불확실성 영역에 대한 집합을 정의합니다.
  2. 각 클래스 마다 평균값을 사용하여 **the class prototype vector** (클래스 중앙값) 를 계산합니다. 
  3. 클래스 중앙값과 가장 비슷한 원소를 찾아서, 그 포인트를 PPL을 위한 포인트라고 확정합니다. 

<img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img5.png?raw=true" alt="drawing" width="900"/>





## 4. Experiment & Result (실험 결과)

### Experimental setup (실험 세팅)

* 데이터셋: source dataset은 [GTA5](https://arxiv.org/pdf/1608.02192v1.pdf) (synthetic dataset)을 사용하였고,  target dataset은 [Cityscape](https://www.cityscapes-dataset.com/)(real-world data)를 사용헀습니다.
* 실험에 사용한 모델은 다음과 같습니다. (1) ResNet101 (2) Deeplab-V2

### Result

<img src="https://github.com/junha1125/Imgaes_For_GitBlog/blob/master/2022-06/img6.png?raw=true" alt="drawing" width="900"/>

- **Figure 1**
  1. LabOR (PPL and SPL) 기법이 기존의 비지도 DA 기법들([IAST](https://github.com/Raykoooo/IAST)) 좋은 성능을 가짐을 확인할 수 있습니다. 
  2. SPL 기법은 지도학습과 거의 유사한 성능을 가지는 것은 굉장히 놀라운 사실입니다.
  3. PPL 은 최근의 준지도 학습법을 사용한 [WDA](https://arxiv.org/abs/2007.15176) 논문보다 더 좋은 성능을 가지는 것을 확인할 수 있습니다.
- **Table 1**
  1. 이 테이블은 최근의 우수한 논문들과의 성능을 수치적으로 비교한 테이블 입니다.
  2. 여기서도, 아주 작은 라벨을 사용하는 준지도 학습법인 PPL과 전체 라벨을 사용하는 지도 학습의 성능이 비슷한 것을 정확히 볼 수 있습니다.
- **Figure 2**
  1. 시각적 성능 차이를 보여줍니다.
  2. 논문에서 제안한 SPL 기법은 다른 기법보다 더욱 정확한 이미지 분할 결과를 만들어주는 것을 확인할 수 있습니다.



## 5. Conclusion (결론)

- 이 논문은 적은 자원의 주석자 사용을 위한 도메인 적응형 이미지 분할 기법을 제안합니다. 
- 2개의 픽셀 선택 기법을 제안하는데, 하나는 영역기반(SPL)이며 하나는 포인트기반(PPL)입니다.
- 한계점 (포스트 저자의 생각)
  1. 논문에 보면 SPL과 PPL 각각 한 이미지당 2.2%의 영역과 40 포인트를 라벨링 한다고 기록되어 있습니다. 이 소리는 충분히 굉장히 적은 라벨을 사용해서 효율적인 기법 처럼 들립니다. 하지만, '내가 만약 주석자 라면?' 이라는 생각을 해봤을 때 (1) PPL 기법의 라벨링은 이미지 전체 라벨링 보다 힘든 작업 같아 보입니다. (섹션 3.3의 이미지 참고) (2) SPL 기법의 40포인트 또한 PPL기법의 포인트 중 하나이므로 대부분 객체 경계의 라벨이라고 고려됩니다. 따라서 이 또한 매우 힘든 작업 같아 보입니다. 
  1. 이것이 정말 효율적이고 자원이 적게 필요한 작업인지는, 좀 더 많은 예제 이미지와 경험담 등을 통해서 비교할 필요가 있어보입니다. 



### Take home message \(오늘의 교훈\)

> 아주 조금의 모델 성능을 올리기 위해서 아주 복잡한 비지도 DA 기법을 사용하는 것보다, 
>비용을 최소한으로 필요로 하는 적은 라벨링만으로 모델의 성능을 매우 효과적으로 올릴 수 있다는 것을 잊지 말아야 한다.

### Take home message \(오늘의 교훈\)

> It may be **more** efficient to obtain a supervision signal at a low cost **than** using complex unsupervised methods to achieve very small performance gains.
>





## Author / Reviewer information

### Author

1. **신인규 \(**Inkyu Shin**\)** 
   * KAIST / RCV Lab
   * https://dlsrbgg33.github.io/
2. **김동진 \(**DongJin Kim**\)** 
   * KAIST / RCV Lab
   * https://sites.google.com/site/djkimcv/
3. **조재원 \(**JaeWon Cho**\)** 
   * KAIST / RCV Lab
   * https://chojw.github.io/



## Reference & Additional materials

1. Citation of this paper
   1. [Towards Fewer Annotations: Active Learning via Region Impurity and Prediction Uncertainty for Domain Adaptive Semantic Segmentation](https://www.semanticscholar.org/paper/Towards-Fewer-Annotations%3A-Active-Learning-via-and-Xie-Yuan/34bc77414f517268e890c8dd31d91d1c65b480cd)
   2. [D2ADA: Dynamic Density-aware Active Domain Adaptation for Semantic Segmentation](https://www.semanticscholar.org/paper/D2ADA%3A-Dynamic-Density-aware-Active-Domain-for-Wu-Liou/6935ed45c7218f236fc6adba7066a395e6c6107f)
   3. [Unsupervised Domain Adaptation for Semantic Image Segmentation: a Comprehensive Survey](https://www.semanticscholar.org/paper/Unsupervised-Domain-Adaptation-for-Semantic-Image-a-Csurka-Volpi/abb79bf15896e0922427ca9d35b0e36ec6718e6e)
   4. [ADeADA: Adaptive Density-aware Active Domain Adaptation for Semantic Segmentation](https://www.semanticscholar.org/paper/ADeADA%3A-Adaptive-Density-aware-Active-Domain-for-Wu-Liou/9371f28a456121815431373fc083072456a1b611)
   5. [MCDAL: Maximum Classifier Discrepancy for Active Learning](https://www.semanticscholar.org/paper/MCDAL%3A-Maximum-Classifier-Discrepancy-for-Active-Cho-Kim/86b13e61d93b1f5c72b834c37ad6c129d6364fa5)
2. Reference for this post
   1. [AdaptSeg](https://arxiv.org/abs/1802.10349) 
   2. [ADVENT](https://arxiv.org/abs/1811.12833)\
   3. [IAST](https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123710409.pdf) 
   4. [Alleviating semantic-level shift](https://arxiv.org/abs/2004.00794), [Active Adversarial Domain Adaptation](https://arxiv.org/abs/1904.07848) 
   5. [Playing for Data](https://arxiv.org/abs/1608.02192)
   6. [DA_weak_labels](https://arxiv.org/abs/2007.15176)
   7. [Maximum classifier discrepancy](https://arxiv.org/abs/1712.02560)
   8. [WDA](https://arxiv.org/abs/2007.15176)



