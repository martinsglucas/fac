[Voltar à página inicial](https://github.com/W4RT1N5/fac#glossário)
# Instruções lógicas de deslocamento
## Deslocamentos (tipo R)
* esquerda: `sll $t0, $s0, 4` (mnemônico shift left logical) (o 4 é shamt - uma constante apesar de ser do tipo R)  
*Obs: o 4 é uma constante (é o shamt), apesar de ser do tipo R*  
*Obs2: $t0 é o rd, $s0 é o rs, o rt é desconsiderado*
* direita: `srl $st0, $s0, 10` (mnemônico shift right logical)
## Lógicas
### AND
Faz o E lógico bit a bit entre `$s0` e `$s1` e salva em `$t0`
```asm
and $t0, $s0, $s1
```
### OR
Faz o OU lógico bit a bit
```asm
or $t0, $s0, $s1
```
### NOR
OU lógico bit a bit e salva o resultado negado
```asm
nor $t0, $s0, $zero
```
### XOR
XOR lógico bit a bit
```asm
xor $t0, $s0, $s1
```
## Aplicações
### Condições compostas
Combinações de condições podem ser feitas com `and` e `or`
### Máscaras
Máscaras são operações para lidar com bits específicos de uma palavra
#### Exemplo: Extrair o bit menos significativo de `$t0`
$t0 = 00110101010...01101  
$t9 = 00000000000...00001  (mascara)  
AND = 00000000000...00001
```asm
and $t2, $t0, $t9
```
# Instruções condicionais
```asm
slt reg, r1, r2 # set on less than
```
Se r1 < r2, reg = 1, senão reg = 0  
Também existe a instrução `slti reg,r1,const` e a instrução `sltu reg,r1,r2` (unsigned)  
A `sltu` pode retornar valores diferentes de `slt` para números negativos
# Instruções de desvio condicional
```asm
beq r1, r2, label # branch if equal
# Instrução do tipo I
```
Se r1 == r2, desvia o fluxo para o linha rotulada por label
```asm
bme r1, r2, label # branch if not equal
# instrução do tipo I
```
Se r1 != r2, desvia o fluxo para o linha rotulada por label

>r1 e r2 são registradores, obrigatoriamente. Se for preciso comparar com uma constante, deve-se carregar a constante em um registrador e comparar com ele

# Como fazer para
## desviar para label se r1 => r2
O complemento de maior ou igual é menor. Então podemos fazer
```asm
slt $t0, r1, r2
beq $t0, $zero, label
# se $t0 = 0, então r1 não é menor que r2. Portanto, r1 >= r2
# essa é a pseudoinstrução bge
```
## desviar para label se r1 > r2
```asm
slt $t0, r2, r1
# para nao precisar carregar um reg = 1 e usar beq, usa-se bme
bme $t0, $zero, label
# se $t0 = 1, então r2 é menor que r1, e, então, r1 é maior que r2
# essa é a pseudoinstrução bgt
```
# Desvio incondicional
```asm
j label # jump
# instrução do tipo J
```
Desvia o fluxo para o label

# ${\color{red}\text{Observações}}$
## Deslocamento relativo ao PC
label se transforma na quantidade de instruções a partir da instrução que está sendo executada

| PC = 1000 | `beq ..., label` |
| :--: | :--: |
| 1004 | |
| 1008 | |
| 1012 | |
| 1016 | |
| 1020 | label |

Logo, label = 5
O endereço de destino do desvio = 4 * label + PC  
O PC guarda o endereço da instrução que está sendo executada no momento