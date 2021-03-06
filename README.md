## Music_VAE_drum_sampling
Music VAE를 이용한 드럼 샘플 생성  

  
  
## Paper
MIDI 데이터에 대해 VAE를 학습한 뒤, 학습 된 latent vector와 디코더를 활용해 interpolation, sampling 등 음악 창작 도구로 활용할 수 있다. 
![image](https://user-images.githubusercontent.com/52783941/108681280-53d1b900-7532-11eb-9ecc-15d98a323fb6.png)

### Bidirectional Encoder
인코더로 2layer-bidirectional LSTM을 사용한다. 이는 인풋 시퀀스의 long-term context를 모델링하는데 적합하다. 인코더 아웃풋은 concat 되어 두개의 FC layer를 거쳐 latent space의 파라미터인 평균과 분산을 추정하게 한다(latent space를 정규분포로 가정했을 때).

### Hierarchical Decoder
이 논문의 contribution, 일반적인 언어 모델보다 음악은 훨씬 긴 data sequence를 갖는다. 디코더에 conductor를 추가해 hierarchical하게 구성함으로써 음악이 갖는 긴 데이터 구조로 인해 생기는 vanishing influence 문제를 해결했다. conductor는 data sequence X가 U개의 subsequence 분리될 수 있다는 가정을 토대로, latent vector Z를 입력 받아 U 차원으로 임배딩한다. 디코더는 이 U개의 벡터를 받아 최종 결과값을 출력한다.

### Sampling
학습이 잘 된 디코더는 정규분포에 가까운 latent vector를 인풋으로 받아 기존 인풋 데이터를 fitting한다. 따라서 정규분포의 랜덤 벡터를 디코더에 통과시키면 새로운 샘플을 무한히 만들 수 있다.

### Model structure
#### Train
- input → 2-layer Bi-LSTM → 2-FC → latent space → FC → 2-layer Uni-LSTM → 2-layer LSTM → output(input)
- 정규분포와 생성된 latent vector와의 KL-divergence를 최소화 하는 방향으로 학습
- 디코더를 통과한 output과 input의 logloss(on/off), mse(velocity, offset)를 최소화 하는 방향으로 학습
   
#### Inference(sampling)
- random gaussian vector → FC → 2-layer Uni-LSTM → 2-layer LSTM → output

  
  
  
## Code
[Groove MIDI Dataset](https://magenta.tensorflow.org/datasets/groove) 사용, 다음과 같이 proto 형식의 sequence로 변환하여 사용한다.  

    notes {
    pitch: 40
    velocity: 120
    start_time: 1.2785327791666667
    end_time: 1.3328806125000001
    is_drum: true
    }

pitch 값은 Roland mapping을 기반으로 MusicVAE는 다음과 같이 단순화된 9차원 mapping을 사용한다.
- Bass, Snare, Closed Hi-Hat, High Floor Tom, Open Hi-Hat, Low-Mid Tom, High Tom, Crash, Ride 
- [Groove_MIDI.ipynb](https://github.com/HyunLee103/Music_VAE_drum_sampling/blob/main/Groove_MIDI.ipynb)에서 이를 시각화했다.

공식 Tensorflow 코드에서는 sequence를 tfrecord 형식으로 변환하여 학습 데이터셋으로 사용한다.
* 전처리 및 학습, samping 과정은 [Music_VAE_tf.ipynb](https://github.com/HyunLee103/Music_VAE_drum_sampling/blob/main/Music_VAE_train.ipynb) 참고.
* 생성된 4마디 드럼 샘플은 generated_samples 폴더에 있다.


```bash
├── README.md                 - 리드미 파일
│
├── generated_samples/        - 생성된 4마디 드럼 샘플
│   ├── sample_1              
│   ├── sample_2             
│   ├── sample_3              
│   ├── sample_4
│
├── Groove_MIDI.ipynb         - groove midi 시각화
│
└── Music_VAE_train.ipynb     - 전처리/학습/샘플링

``` 
  
  



  
  
## Reference
- A Hierarchical Latent Vector Model for Learning Long-Term Structure in Music(2018, Google Magenta)
- https://github.com/magenta/magenta/tree/master/magenta/models/music_vae
