# Analiza în frecvență a mișcării verticale a elicopterului

## Scopurile propuse 
1. Analizez cum se deformează hodograful procesului la perturbări.
1. Ajustez hodograful prin noi sisteme care compenseaza starea inițială.
1. Analizez răspunsul sistemului la armonici.
1. Proiectez sisteme noi pentru ajustarea diagramelor Bode ale procesului.

1.) Incep cu declarearea vectorului de pulsatii  
```bash
omeg = logspace(-2, 2, 1000)’;
``` 
ce selectează banda frecvențială de interes pentru sistemul meu.

Memorez in ```inters1``` partea reala a punctului in care hodograful lui P_tan intersecteaza axa OX in dreapta axei imaginare pe care o pot calcula astfel: 
```python
inters1 = evalfr(P_tan,omeg);
```
Memorez in ```inters2``` partea reala a punctului in care hodograful lui P_tan intersecteaza axa OX in stanga axei imaginare , valoare pe care o iau de pe graficul Nyquist.
```real```
```python
nyquist(P_tan, omeg);
inters2 = -0.172;
```

Realizez diagrama Nyquist pentru  P_tan * 2 pentru intersectia hodografului cu axa OX in stanga axei imaginare:
```javascript
nyquist(P_tan*2, omeg);
inters3 = -0.344;
```

Realizez diagrama  Nyquist a lui P_tan * exp(-1i * pi / 4) ¸si memorez in ```inters4``` partea reala a punctului in care hodograful intersecteaza axa OX in stanga axei imaginare.
```python
nyquist(P_tan * exp(-1i * pi / 4),omeg);
inters4 = -0.226;
```

Figurez diagrama Nyquist pentru P_tan * 1/s, unde observ ca hodograful are o asimptota verticala. In variabila ```asimpt``` am memorat coordonata de pe OX a acestei asimptota.

```python
s = tf('s');
nyquist(P_tan*1/s,omeg);
asimpt = -0.125;
```

2.) Fie ```C_1 = tf(K_1, [T_1 1]);``` in care  ```K_1``` si ```T_1``` sunt doi scalari reali.

Pentru a afla ```K_1``` inmultesc cu 100 partea fara coeficient a numitorului apoi impart la numarator.

```python
K_1 = (P_tan.den{1}(4) * 100 / P_tan.num{1}(4));
```
ceea ce va asigura faptul ca graficul treca in dreapta axei imaginare prin punctul(100,0)

```python
T_1 = 900;
C_1 = tf(K_1, [T_1 1];
nyquist(P_tan*C_1,omeg);
```
 ceea va asigura si faptul ca intersecteaza axa OX intre -0.5 si 0.

O alternativa este ```C_2 = K_2 * tf([1 1], [T_2 1]);```
Pentru ca P_tan * C_2 sa intersecteze OX intre -0,5 si 0; sa treaca in dreapta axei imaginare in (100,0) si sa nu intersecteze cercul unitate in cadranele II si III scriu: 
```python
K_2 = K_1;
T_2 = 1900 
C_2 = K_2 * tf([1 1], [T_2 1]);
hold on; 
nyquist(tf([-1 1], [1 1]), omeg);
```

3.) Diagrame Bode
Fie u(t) = 7sin(t + pi/4) armonica pe care o aplic la intrarea procesului.

a) Figurez Diagramele Bode a lui P_tan. 
In variabilele ```amp1``` si ```def1``` memorez amplitudinea si defazajul iesirii lui P_tan atunci cand am intrarea u(t).
```python
bode(P_tan,omeg);
[ampa defa]  = bode(P_tan,omeg);
amp1 = 7 * ampa(500);
def1 =def(500) + 45;
```

b) Figurez Diagramele Bode a lui 3 * P_tan.
In variabilele ```amp2``` si ```def2``` memorez amplitudinea si defazajul iesirii lui 3*P_tan atunci cand am intrarea u(t).
```python
bode(3*P_tan,omeg);
[ampb defb] =  bode(3*P_tan,omeg);
amp2 = 7 * ampb(500);
def2 = defb(500) + 45;
```

c) Figurez Diagramele Bode pentru  exp(-1i * pi / 6) * P_tan.
In variabilele ```amp3``` si ```def3``` memorez amplitudinea si defazajul iesirii lui exp(-1i * pi / 6) * P_tan cand la intrare am u(t).
```python
bode(exp(-1i * pi / 6) * P_tan,omeg);
[ampc defc] = bode((exp(-1i * pi / 6) * P_tan),omeg);
amp3 = 7 * ampc(500);
def3 = defc(500) + 45;
```

d) Figurez Diagrama Bode pentru 100*P_tan si memorez in ``` omeg_1```  pulsatia la care valoarea ampliﬁcarii se apropie cel mai mult de 1 (0 dB).
```python
bode(100*P_tan,omeg);
omeg_1 = omeg(643);
```

e) Figuez Diagrama Bode pentru 100*P_tan si memorez in ```omeg_2``` pulsatia la care valoarea fazei se apropie cel mai mult de −180 de grade. 
```python
bode(100*P_tan,omeg);
omeg_2 = omeg(575);
```

4.) Vreau sa ajustez caracteristica frecventiala a lui P_tan, de aceea creez:
```C_3 = tf (K_3 * w_3, [1 w_3]);```

Cu ```K_3``` si ```w_3``` astfel incat P_tan * C_3 sa inceapa din 40 dB si  sa descreasca astfel incat, la pulsatia de 0.2 rad/s, sa ﬁe cu 3 dB sub valoarea asimptotei de la joasa frecventa.
```python 
K_3 = K_1;
w_3 = 0.2;
C_3 = tf (K_3 * w_3, [1 w_3]);
bode(P_tan * C_3);
```

La sfarsit atenuez varful de pe caracteristica de amplificare a sistemului.

Fie ```C_4 = 100 * tf(B_4 * [1 A_4], A_4 * [1 B_4]);```
Figuez Diagramele Bode ale lui P_tan * C_4 in care determin A_4 si B_4 in asa fel incat faza lui P_tan * C4 in omeg_2 sa fie intre -120 si -150 de grade.
```python 
A_4 = 0.595;
B_4 = omeg_2^2/A_4;
C_4 = 100 * tf(B_4 * [1 A_4], A_4 * [1 B_4]);
[amp4c def4c] = bode(P_tan*C_4, omeg);
```

