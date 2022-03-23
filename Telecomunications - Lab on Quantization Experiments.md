#
### A. Uniform Quantization
**A.1 ** Display the input-to-output mapping characteristic of a 2-bit uniform quantizer:
```matlab
quant_ch(2, 'uniform')
% Adjust axis to match figure
xticks(-1:0.25:1)
yticks(-1:0.2:1)

% output png figure
print('figure 5.1.png', '-dpng', '-S1280,720')
```
>![](figure%205.1.png)

**A.2 **
Let $x$ be the analog pulse amplitude, and $x_q$ be the quantizer output, if x is the input. Assume that pulse amplitude $x$ is normalized such that $|x| \leq 1$. From Graph 5.1 derive the quantizer mapping rule:
| Quantization Interval | $x_q$ | Binary Code |
| :----: | :----:| :----:|
| $[\frac{1}{2}, 1)$ | $\frac{3}{4}$| 1 |
| $[0,\frac{1}{2})$ | $\frac{1}{4}$| 0 |
| $(0,-\frac{1}{2}]$ | -$\frac{1}{4}$| 0 |
| $(-\frac{1}{2}, -1]$ | -$\frac{3}{4}$| 1 |

Observe the length of each interval that appears in the first column of Table 5.1. ==How is the quantization interval length related to the number of quantization levels of a uniform quantizer?== The entries in the 2nd column are referred to as quantization levels; only these values appear at the quantizer output.
> Quantization Interval $\Delta v = 2m_p/L$
> $\Delta v = \frac{1}{2}$
> Também define a relação entre como o intervalo se relaciona com a quantidade de niveis de quantização



**A.3** Generate the 8 element sampled sequence $x$ where each element of $x$ represents the analog pulse amplitude at that sampling instant:
```matlab
clear; clf; 

x = [ 0.8, 0.6, 0.2, -0.4, 0.1, -0.9, -0.3, 0.7 ];

% We transpose the result of `quantize` to see the result as a column
xq = quantize(x, 2)' 
ans =
   0.7500
   0.7500
   0.2500
  -0.2500
   0.2500
  -0.7500
  -0.2500
   0.7500
```
| Element | $x$ | $x_q$ | Binary  Code |
| :----: | :----: | :----: | :----: |
| 1|0.8|0.75|1|
| 2|0.6|0.75|1|
| 3|0.2|0.25|0|
| 4|-0.4|-0.25|0|
| 5|0.1|0.25|0|
| 6|-0.9|-0.75|1|
| 7|-0.3|-0.25|0|
| 8|0.7|0.75|1|
> Verificando com a tabela
> $0.8 \in [\frac{1}{2}, 1)$ -> $\frac{3}{4}$ e $(1)_2$ ✔
> $0.6 \in [\frac{1}{2}, 1)$ -> $\frac{3}{4}$ e $(1)_2$ ✔
> $0.2 \in [0,\frac{1}{2})$ -> $\frac{1}{4}$ e $(0)_2$ ✔
> ...

**A.4 **  Devise a rule which assigns a unique 2-bit binary symbol to each quantization level. Use a natural code1 which assigns the binary symbol "00" to the most negative quantization level (0:75 in this example).
As you move from the most negative to the most positive quantization
level, numeric value of the binary symbol is incremented. This procedure is called source coding. In Table 5.1, fill in the 3rd column with
2-bit natural code. Apply this coding procedure to the quantized sequence $x_q$ and enter the binary codes into the 3rd column of Table
5.2. You may check your answer with the MATLAB function bin enc
(natural binary encoder):
```matlab
xbin = bin_enc(xq, 2)
xbin =
 
   1   1
   1   1
   1   0
   0   1
   1   0
   0   0
   0   1
   1   1
```
> Regra  em pseudocódigo
> ```
> func(xq):
>  vec = [];
>   for x in xq:
>      if x < 0.5 && x >= 0:
>      x = [x ; 1 0]
>      elif x >= 0.5:
>      x = [x; 1 1]
>      elif x <0 && x > -0.5:
>      x = [ x: 0 1]
>      else:
>      x= [x; 0 0]
> ```

**A.5** One of the most common methods of converting an analog signal into a stream of binary digits is *pulse code modulation* (PCM). In PCM, you perform the following sequence of operations:  
- sampling;
- quantization;
- source coding;
- conversion into a serial data stream;  


Thus, if the source coded variable xbin is converted into a serial data, you in fact, generate PCM data representing the sequence x:
```matlab
par2ser(xbin)
ans =

   1   1   1   1   0   1   1   0   0   1   0   0   1   0   1

```

### B. Distortion Induced by Quantization

**B.1** Quantization generates an approximation to the original sequence. To observe this point generate samples from a sinusoid of unit amplitude:  
Since the quantized sequence is only an approximation to the original, quantization produces distortion. There are two types of distortion associated
with a quantizer: overload (or clipping) distortion and quantization noise.
Overload distortion occurs when the input signal exceeds the quantizer's input range - in this experiment [ 1; 1 ]. Once the quantizer range has been
exceeded, quantizer output will remain at its maximum or minimum value
until the input falls within the quantizer's input range
```matlab
start; % função start para set de ambiente
start;
x = sin( 2*pi*20*[1:400]/SAMPLING_FREQ);
subplot(2, 3, 1);
waveplot(x), hold on, waveplot(quantize(x,2));
subplot(2, 3, 2);
waveplot(x), hold on, waveplot(quantize(x,3));
subplot(2, 3, 3);
waveplot(x), hold on, waveplot(quantize(x,4));
subplot(2, 3, 4);
waveplot(x), hold on, waveplot(quantize(x,5));

print('figure b1.png', '-dpng', '-S1280,720');


```
>![](figure%20b1.1.png)

> Overload observável em 5 bits

**B.2** Set the amplitude of the sinusoid x to 0.9 and display x and its 3-bit quantized version
> Observe that, since max jxj = 0:9 < 1, no clipping occurs. Now increase the signal amplitude and repeat the above step for a = 1:25; 2 and 5.
```matlab
start;

x = sin( 2*pi*20*[1:400]/SAMPLING_FREQ);
a = 0.9;
subplot(2, 3, 1);
waveplot(a*x), hold on, waveplot(quantize(a*x,3));
% a = 1.25
a = 1.25;
subplot(2, 3, 2);
waveplot(a*x), hold on, waveplot(quantize(a*x,3));
% a = 2
a = 2;
subplot(2, 3, 3);
waveplot(a*x), hold on, waveplot(quantize(a*x,3));
% a = 5
a = 5;
subplot(2, 3, 4);
waveplot(a*x), hold on, waveplot(quantize(a*x,3));

print('figure b2.png', '-dpng', '-S1280,720');
```
>![](./figure%20b2%202.png])

**B.3**  Use samples from the sinusoid x to observe quantization error $x - x_q$:
```matlab
clf;
xq = quantize(x, N); 
xe = x - xq
waveplot(xe);
```

where N = 2, 3, 4 and 5. For each choice of N, record the maximum absolute value of the quantization error into Table 5.3.
```matlab
start;
x = sin( 2*pi*20*[1:400]/SAMPLING_FREQ);
xq = quantize(x, 2);
xe = x - xq;
mx_mod = max(abs(xe))
mx_mod= 0.2500
% Repete operação para 3, 4, 5
```

|  N  |  2 | 3 | 4 | 5 |
| ---- | ---- | ---- | ---- | ---- |
| $max \|xe\|$ |  0.25 | 0.125 | 0.0625 | 0.03125 |

**B.4** Generate the following sequence and display its PSD function:
```matlab
x = sin( 2*pi*512*[1:2048]/SAMPLING_FREQ);
psd(x)
```
![[figure b4.png]]
For N = 2; 3; 4; 5; and 6 display the PSD function of the quantized waveform xq and evaluate the corresponding quantization error power in dB. Enter results into Table 5.4.
```matlab
xq = quantize(x, 2);
psd(xq);
sq2 = 10*log10(var(x - xq))
sq2 = -15.819

% Repete para 2, 3, 4, 5 e 6
```
|  N  |  2 | 3 | 4 | 5 | 6 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| $\sigma_q^2$ |  -15.819 | -22.105 | -28.332 | -34.496 | -40.621 |
Agora usando:
$$\sigma_q^2 = -(4.77 + 6.02\times N)dB$$

|  N  |  2 | 3 | 4 | 5 | 6 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| $\sigma_q^2$ | -16.810| -22.830 |  -28.850 | -34.870 | -40.890 |

Comparação

|  N  |  2 | 3 | 4 | 5 | 6 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Modulo da diferença (sigma) | 0.991| 0,725 |  0,518 | 0,374 | 0,269 |

**C.1** 1 Generate 100 samples representing voiced segment of a typical speech signal and display the sequence:
```matlab
s = speech(100)
subplot(211), waveplot(s)

```
![[figure c1.png]]

Observe that the signal amplitude is restricted to [ 1; 1 ] and there
are samples in s with amplitude 1. Thus, s spans the entire input
range of the quantiz

**C.2** Quantize s using a 6-bit uniform quantizer and display the quantized waveform:
```matlab
sq = quantize(s,6);
subplot(212), waveplot(sq)
```
![[figure c2.png]]
The quantized waveform sq closely resembles the original sequence s. Now, scale s by a factor K such that $max |Ks| \leq 0.01$.
```matlab
x = normalize(s, 0.01);
xq = quantize(x,6);
subplot(211),waveplot(x),subplot(212),waveplot(xq);
```
![[figure c2.2.png]]
> A diferença de xq para s ou sq é a quantização, s e sq são dados em forma continua, xq é sua representação binária

**C.3** **Non-uniform quantization**: Display the quantizer characteristic of a $\mu$-law companding 3-bit quantizer:	
```matlab
clf;
quant_ch(3, 'mu_law')
```
![[figure c3.png]]

**C.4** Apply $\mu$-law companding to sampled signals s and x and then quantize:
```matlab
msq = mu_inv( quantize(mu_law(s), 6) );
mxq = mu_inv( quantize(mu_law(x), 6) ;

```
Since quantized samples must be restored to their original values, nonuniform quantization is equivalent 1 to the following sequence of operations: *compand* - *uniform quantization* - *expand*. Display waveform msq, mxq and compare with original signals s and x. It is of particular interest to compare msq with sq and mxq with xq. First, the large amplitude signal s:
```matlab
clf, subplot(311), waveplot(s)
subplot(312), waveplot(sq)
subplot(313), waveplot(msq)
```
![[figure c4.1 2.png]]

Now for the small amplitude signal x:
```matlab
figure(2), subplot(311), waveplot(x)
subplot(312), waveplot(xq)
subplot(313), waveplot(mxq)
```
![[figure c4.2.png]]
 
 **C.5** For a given number of quantization levels, a uniform quantizer is optimum if input sample amplitudes are uniformly distributed over the
input range [ 1; 1 ]. However, speech signal amplitude distribution is
best modelled by a Laplace distribution. Generate the sample pdf of
2,000 samples from a Laplace distribution with variance 0.01
```matlab
close(2), figure(1), clf;
a = laplace( 2000, 0.01 );
pdf( a, 30 );
```
![[figure c5.png]]
Compand the sequence a by a -law compander and display the sample pdf of the resulting sequence
```matlab
b = mu_law(a);
hold on, pdf( b, 30);
```
![[figure c5.1.png]]
The pdf of the companded sequence b resembles a uniform pdf, which results in better utilization of available quantization levels

**C.6** In practise, any quantizer has to operate on signals at varying amplitude levels. Therefore, to assess the quantizer performance, it must be tested with signals at diferent power levels. One measure of quantizer performance is the signal to quantization noise ratio SQNR. Consider a 8-bit quantizer used in a typical voice grade A/D converter:
> Algorithm
> 1. Generate a sequence of 1,000 voice signal samples at a specied power level and measure the signal power: $\sigma_s^2$.
> 2. Quantize the sequence using an 8-bit uniform quantizer. Measure the resulting quantization error power:  $\sigma_s^2$ (uniform).
> 3. Quantize the sequence using a -law companding 8-bit quantizer. Measure the resulting quantization error power: $\sigma_q^2$($\mu$-law).
> 4. Measure the SQNR = $\sigma_s^2/\sigma_q^2$ for both quantizer types.

This algorithm is available in MATLAB function exp5 c6
```matlab
 power = [ 0.0001 0.001 0.01 0.1 1 ];
exp5_c6( power );
```
where all quantities are measured in dB. Enter results into Table 5.5. Also plot SQNR vs. $\sigma_s^2$ or both quantizer types in Graph 5.2.

| $\sigma_s^2$ | SNQR(uniform) | SQNR($\mu$-law)  |
| ---- | :----: | :----: |
|-39.2457 |  13.4469 |  36.2628|
|-29.7019 |  23.2393 |  37.5571|
|-20.0318  | 33.0449 |  37.4675|
|-10.6034  | 42.1210 |  37.5378|
|  -3.8564 |  47.2666  | 34.7939

![[figure c.6.png]]