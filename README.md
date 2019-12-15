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
