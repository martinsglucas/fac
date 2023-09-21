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
X = Xn-1 Xn-2 ... X2 X1 X0

Considerando uma base b:  
X = Xn-1 * b^(n-1) + Xn-2 * b^(n-2) + ... + X2 * b² + X1 * b¹ + X0 * 1  

Exemplo: 101001 na base 2  
X = 1*2^5 + 0*2^4 + 1*2^4 + 0*2^3 + 0*2^2 + 0*2^1 + 1*2^0
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
