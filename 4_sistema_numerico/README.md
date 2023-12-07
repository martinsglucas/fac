[Voltar à página inicial](https://github.com/martinsglucas/fac#sumário)
# Sistema numérico
**Posicional:**
Nosso sistema numérico é posicional. Exemplo
| C | D | U |
| :-: | :-: | :-:
| 1 | 0 | 0 |

O mesmo símbolo possui valores distintos a depender da posição que ocupa

**Possui base:**
Quantidade de símbolos para representação 

Considerando o número  
$X = X_{n-1}X_{n-2} \cdots X_{2}X_{1}X_{0}$

Considerando uma base b:  
$X = X_{n-1} * b^{n-1} + X_{n-2} * b^{n-2} + \cdots + X_{2} * b^2 + X_{1} * b^1 + X_{0} * b^0$

<!--$\forall x \in X, \quad \exists y \leq \epsilon$-->

Exemplo: 101001 na base 2  
$X = 1*{2^5} + 0*{2^4} + 1*{2^4} + 0*{2^3} + 0*{2^2} + 0*{2^1} + 1*{2^0}$

## Representação de sinal (binário)
### Complemento de 2
| | 0 | 0 | 1 | 0 | 1 | 0 | 1 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
| | 1 | 1 | 0 | 1 | 0 | 1 | 0 |
| + | ||||||1
| | 1 | 1 | 0 | 1 | 0 | 1 | 1 |
### Bit de sinal
Se o bit mais significativo for 0, o número é positivo. Se o bit mais significativo for 1, o número é negativo
### Exercício
Relembrar outras formas de representação de números com sinal, em particular:
* bit de sinal (magnitude)
* complemento a 1
* excesso
# Representação Numérica
* possui base
* é posicional
* Processador MIPS 32 possui registradores de 32 bits (4 bytes). Qual a capacidade de representação?
## Números sem sinal (unsigned int)
menor: 00000...000  
maior: 11111...111  
$2^0 + 2^1 + \cdots + 2^{31}$
* A representação com 32 bits vai de 0 a $2^{32} - 1$
<!--
$$
   f(x,y) = \sin(x+y)
$$
-->
## Números com sinal (int)
É possível representar de $-2^{31}$ a $2^{31} - 1$  
A representação com 32 bits vai de $-2147.483.648$ a $2147.483.647$
## Observação 1  
O MIPS 21 não implementa o long mas a ideia seria usar 2 registradores , o que nos dá:  

unsigned long: 0 a $2^{64}-1$  
long: $-2^{63}$ a $2^{63}-1$
## Observação 2
No Assembly MIPS, as instruções interpretam o binário como um número **inteiro com sinal**. Algumas instruções possuem variantes terminadas por `u` que interpretam os inteiros como um número **sem sinal**. Exemplo:

```asm
addu $t0, $t1, $t2
addiu $t0, $t1, 1000
```
