# Edge-AI-for-ECG-Sensor
* Edge AI use-case for sensor Synthetic Noise addition for &amp; high accuracy purposes.<br />
* As Sensors are being used in various industries, it's being used in various situations.<br />
* In various situations, sensors are not capable to give you an accurate signal as it's disrupted.<br />
* In order to solve this problem, they needs to embedded with small size edge-AI to give you the reasonable signal.<br />
* In this repository, we're going to focus on solving problems at medical field (ECG Sensor).<br />
* ECG sensor is being used for detecting and recording the electrical activity of the heart, used in majority of the ER (Emergency) cases.<br />
* As ECG sensor is being used in 79% of ER cases (122 million patients per year), it's being used in various situations and perform badly in 3% ~ 5% (3.7 million to 6.1 million patients per year) for respiration state (The process by which organisms produce energy from food), physical movement, and electrode contact issues (wrong placement which often happens).<br />
* IBM Granite models brings a solution here. They are great when placing edge deployment with Synthetic noise application and embedding with small hardware products.<br />
* For ECG Sensor deployment, we decided to use IBM Granite TSPulse (granite-timeseries-tspulse-r1).<br />
* TSPulse is one of the best AI model for this use-case because it is pretrained with *dual-space masked recontruction* (time + frequency domains), making it naturally strong at imputation and Synthetic noise addition especially on physiological signals.<br />
* Use the *hybrid-dualhead-512-p8-r1* variant for imputation/denoising. It excels at zero-shot reconstruction and works perfectly with fine-tuning.<br /><br />
  
## Software Use-case
* Use *Google Colab* when training AI model and generate API code for simple integration(Compatibility for majority of the embedding engineers out there).<br />
* Real-time Arduino integration: 512-1024 samples and send data via Serial/WiFi.<br /><br />

## Synthetic Noise Application
* *Gaussian white noise* (for powerline/thermal interference).<br />
* *Baseline Wander* (low-frequency drift via sine square waves).<br />
* *EMG/muscle artifacts* (high-frequency modulated Gaussian) to train the TSPulse model on noisy-clean ECG pairs.<br /><br /><br />

## Algorithm & Mathematical Equation Use-case<br />
<table>
  <thead>
    <tr>
      <th>Noise Type</th>
      <th>Real-World Cause</th>
      <th>Standard Mathematical Model</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Additive White Gaussian Noise (AWGN)</strong></td>
      <td>Thermal noise, quantization error, amplifier noise</td>
      <td><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}n_{\text{AWGN}}(t)%20=%20\sigma_g%20\cdot%20w(t),\quad%20w(t)\sim\mathcal{N}(0,1)" alt="AWGN1" /><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}\sigma_g%20=%20\sqrt{\frac{P_s}{10^{\text{SNR[dB]}/10}}}" alt="AWGN2" /></td>
    </tr>
    <tr>
      <td><strong>Baseline Wander (BW)</strong></td>
      <td>Respiration, body movement, electrode drift</td>
      <td><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}n_{\text{BW}}(t)%20=%20A_{\text{bw}}\sin(2\pi%20f_{\text{resp}}%20t)%20\quad\text{or multi-harmonic}" alt="BW" /><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}\sum_{k=1}^{3}b_k\sin(2\pi%20k%20f_{\text{resp}}%20t%20+%20\phi_k)" alt="BW multi" /></td>
    </tr>
    <tr>
      <td><strong>Powerline Interference (PLI)</strong></td>
      <td>50/60 Hz electromagnetic coupling</td>
      <td><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}n_{\text{PL}}(t)=A_{\text{pl}}\sum_{k=1}^{H}c_k\sin(2\pi%20k%20f_{\text{pl}}%20t%20+%20\phi_k)" alt="PLI" /></td>
    </tr>
    <tr>
      <td><strong>Muscle Artifact (MA/EMG)</strong></td>
      <td>Skeletal muscle contraction, tremor, shivering</td>
      <td>Gold standard: MIT-BIH NSTDB “ma” record (real recorded EMG)<br><br><strong>Synthetic model (most realistic & widely used):</strong><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}n_{\text{MA}}(t)%20=%20\sigma_{\text{ma}}(t)\cdot%20g(t)" alt="MA" /><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}g(t):%20\text{band-pass 20-500 Hz Gaussian noise}" alt="g(t)" /><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}\sigma_{\text{ma}}(t):%20\text{low-freq envelope (0.1-10 Hz)}" alt="envelope" /></td>
    </tr>
    <tr>
      <td><strong>Electrode Motion Artifact (EM)</strong></td>
      <td>Skin stretching, loose contact, cable movement</td>
      <td>Gold standard: MIT-BIH NSTDB “em” record (real recording)<br><br><strong>Synthetic model (biphasic exponential pulse train):</strong><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}n_{\text{EM}}(t)%20=%20\sum_i%20A_i%20\left(e^{-(t-t_i)/\tau_1}%20-%20e^{-(t-t_i)/\tau_2}\right)" alt="EM" /><br><img style="vertical-align:middle;" src="https://latex.codecogs.com/svg.latex?\dpi{200}\color{white}\tau_1%20\gg%20\tau_2%20\text{ (sharp rise + slow decay)}" alt="tau" /></td>
    </tr>
  </tbody>
</table>
