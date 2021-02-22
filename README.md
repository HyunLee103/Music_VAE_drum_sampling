# Music_VAE_drum_sampling
Music VAE를 이용한 drum 샘플 생성

## Paper
MIDI 데이터에 대해 VAE를 학습한 뒤, 학습 된 latent vector를 활용해 interpolation, sampling 등 음악 창작 도구로 활용할 수 있다. 
![image](https://user-images.githubusercontent.com/52783941/108681280-53d1b900-7532-11eb-9ecc-15d98a323fb6.png)

### 1. Bidirectional Encoder
인코더로 2layer-bidirectional LSTM을 사용한다. 이는 인풋 시퀀스의 long-term context를 모델링하는데 적합하다. 인코더 아웃풋은 concat 되어 두개의 FC layer를 거쳐 latent space의 파라미터인 평균과 분산을 추정하게 한다.(latent space를 정규분포로 가정했을 때)






## Reference
- A Hierarchical Latent Vector Model for Learning Long-Term Structure in Music(2018, Google Magenta)
- https://github.com/magenta/magenta/tree/master/magenta/models/music_vae
