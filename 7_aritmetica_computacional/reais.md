# Representação de números reais
A representação de reais parte da notação científica de um número. Um número está em notação científica se estiver na forma:
$$ \pm F \times B^E$$
onde:
- F é a fração (significando ou mantissa)
- B é a base numérica da representação
- E é o expoente

Exemplo: O número $ 3155760000000 = 3,155760 \times 10^{12}$

Em binário, a representação é a mesma, e os números à direita da vírgula são potências negativas de 2. Exemplo: $ 0,1_{2} = 2^{-1} = 0,5_{10}$ ou $5,5_{10} = 101,1_2$

Um número em notação científica pode ser:
- normalizado: 
    - quando a mantissa está entre 1 e a base. 
    - Ex: $2,34 \times 10^2$. 
    - $0 \leq F \leq B $
- não normalizado: $984,32 \times 10^5$

Em binário, um número normalizado é da forma $1,xxxx \times 2^E$ em que $xxxx$ é a fração

O padrão de representação de números reais adotado mais amplamente é o padrão IEEE 754, que admite dois formatos: **precisão simples** e **precisão dupla**.

## Precisão Simples

Conhecido como **float**

| S (msb) | Expoente | Fração |
|:--:|:--:|:--:|
| 1 bit | 8 bits | 23 bits |

Registrador de 32 bits = $(-1)^S \times (1+F) \times 2^{Exp}$
- S é o bit de sinal
- Fração é o que vem à direita do ponto binário (já que à esquerda é sempre 1)
- Expoente é um número com sinal no **padrão excesso** (não é complemento a dois)

### Representação do Expoente

$x = 101,1 \times 2^{-6} = 1,011 \times 2^{-4}$

$y = 1,1 \times 2^{3}$

O 4 em binário é 0100. O -4, em complemento de 2, é 1100

Logo, esse número seria representado como:

|| S | Expoente | Fração |
|:--:|:--:|:--:|:--:|
|x = | 0 | 1111 1100 | 01100000...000 |
|y = | 0 | 0000 0011 | 10000000...000 |

O problema aqui é que **x é menor que y, mas armazenado no registrador x fica maior que y. Essa discrepância é indesejável**

### Representação do sinal por Excesso
Nessa representação, todos os números são positivos. Para encontrar, o valor real desse número, basta subtrair o excesso.

| | | |
| :--: | :--: | :--: |
| -8 | | 0 |
| -7 | | 1 |
| -6 | | 2 |
| -5 | | 3 |
| -4 | | 4 |
| -3 | | 5 |
| -2 | $\to$ <br> +8 <br> excesso <br> -8 <br> $\gets$ | 6 |
| -1 | | 7 |
| -0 |  | 8 |
| 1 |  | 9 |
| 2 |  | 10 |
| 3 | | 11 |
| 4 | | 12 |
| 5 | | 13 |
| 6 | | 14 |
| 7 | | 15 |

O excesso é chamado **bias**

Considerando, para precisão simples, um bias de 127, teríamos no exemplo:

$x = 1,011 \times 2^{-4} \implies exp = -4 + 127 = 123$

$y = 1,1 \times 2^{3} \implies exp = 3 + 127 = 130$

$x < y$

|| S | Expoente | Fração |
|:--:|:--:|:--:|:--:|
|x = | 0 | 0111 1011 | 01100000...000 |
|y = | 0 | 1000 0010 | 10000000...000 |

$x < y$

### Capacidade de representação
Os expoentes 0 e 255 são reservados (o expoente vai de 1 a 254)

|| S | Expoente | Fração |
|:--:|:--:|:--:|:--:|
|menor = | 0 | 0000 0001 <br> 1 - 127 = -126 <br> $= \pm 1,0 \times 2^{-126} \approx \pm 1,2 \times 10^{-38}$ | 00000000...000 |
|maior = | 0 | 1111 1110 <br> 254 - 127 = 127 <br> $= \pm 1,1111...111 \times 2^{127} \ \approx 2 \times 2^{127} \approx 3,8 \times 10^{+38}$ | 11111111...111 |

## Precisão Dupla

Conhecido como **double**

Registrador de 64 bits

Bias de 1023

<div>

| S (msb) | Expoente | Fração |
|:--:|:--:|:--:|
| 1 bit | 11 bits | 20 bits |

| Fração |
|:--:|
| 32 bits |
</div>

Fração de 52 bits

### Capacidade

menor = $ 1,0 \times 2^{-1022} \approx \pm 2,2 \times 10^{-308}$

maior = $1,1111...111 \times 2^{1023} \approx 2 \times 2^{1023} \approx 1,8 \times 10^{+308}$

## Números Especiais

| Expoente | Fração | Descrição |
|:--:|:--:| :--: |
| 0 | 0 | zero | 
| 0 | não zero | $\pm \text{ número desnormalizado}$ | 
| 1 a 254 <br> 1 a 2046 | qualquer coisa | $\pm \text{ número normalizado}$ | 
| 255 ou 2047 | 0 | $\pm \text{ infinito}$ | 
| 255 ou 2047 | não zero | Not a Number (NaN) | 

# Adição em Ponto Flutuante

Exemplo: $9.999 \times 10^{1} + 1.610 \times 10^{-1}$

1. Alinhar o ponto
- dar shifts à direita no número com menor expoente (para cada shift, soma 1 ao expoente)
- do exemplo: $9.999 \times 10^{1} + 0.0161 \times 10^{1}$
2. Somar os significandos
- do exemplo: $10.105 \times 10^{1}$
3. Normalizar o resultado
- do exemplo: $1.0105 \times 10^2$

# Multiplicação em Ponto Flutuante

Exemplo: $1.0 \times 2^{-1} \quad \times \quad -1.11 \times 2^{-2}$
1. Soma-se os expoentes
- Sem o bias = -1 -2 = -3
- Com o bias = (- + 127) + (-2+127) = -3 + 254 - 127 = -3 + 127
2. Multiplica os significandos
- do exemplo: $1.0 \times 1.11 = 1.11 \times 2^{-3} $
3. Normaliza o resultado e ajusta o sinal

# Instruções em Assembly MIPS
A arquiterura MIPS traz um hardware próprio para manipular números de ponto flutuante. Este hardware inclui:
- instruções próprias
32 registradores `$f0`, `$f1`, `$f2`, ..., `$f31`
    - podem ser usados individualmente para precisão simples
    - são usados aos pares para precisão dupla (`$f0/f1`, `$f2/f3`, ..., `$f30/31`). Nesse caso, **o registrador de número par que é referenciado**

As instruções terminam sempre por:
- .s para precisão simples
- .d para precisão dupla

## As principais são

### Aritméticas

- add.s / add.d
- mul.s / mul.d
- sub.s / sub.d
- div.s / div.d

```mips
# Exemplo
add.d $f0, $f8, $f10
# f0 = f8 + f10
```

### Acesso à memória
- l.s / l.d : load (carrega)
- s.s / s.d : store (armazena)
```mips
# Exemplo
l.d $f0, 0($s0)
l.s $f0, label (é uma pseudo)
```
### Desvios condicionais
Faz-se em duas etapas:
- Comparação dos valores de registradores, com resultado salvo em um registrador especial
- Desvio feito com base no valor do registrador especial

#### Comparação

- c.eq.s / c.eq.d : =
- c.ne.s / c.ne.d : !=
- c.lt.s / c.lt.d : <
- c.le.s / c.le.d : <=
- c.gt.s / c.gt.d : >
- c.ge.s / c.ge.d : >=

```mips
# Exemplo: verifica se $f0 < $f1
c.lt.s $f0, $f1
```

#### Desvio
- bc1t label: se verdadeiro
- bc1f label: se falso
```mips
# Do exemplo acima
c.lt.s $f0, $f1
bc1t labelSeVerdadeiro
bc1f labelSeFalso

```

## Exemplo

```c
float f2c (float fahr){
    return (5.0/9.0)*(fahr 32.0)
}
```

```mips
# Considerar o fahr em $f0
.data
c5: .float 5.0
c9: .float 9.0
c32: .float 32.0
.text
f2c:
l.s $f1, c5
l.s $f2, c9
l.s $f3, c32
div.s $f10, $f1, $f2 # f10 = 5.0/9.0
sub.s $f0, $f0, $f3 # f0 = fahr - 32.0
mul.s $f0, $f0, $f10 # f0 = temp em C
```