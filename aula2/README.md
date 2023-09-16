## Escrevendo um programa em assembly MIPS
Um programa em assembly MIPS possui o seguinte esqueleto
```asm
.data # seção de dados
.text # seção de código
main: # seção de código, rótulo main
```
*Obs: rótulo é um apelido para o endereço de memória*  
A execução do código começa na linha rotulada por `main` e segue linha após linha (salvo de houver um desvio). A esse paradigma chamamos sequencial
### Tipos de dados
* .word W1, W2, ..., Wm : dados de 32 bits
* .byte B1, B2, ..., Bm : dados de 8 bits
* .asciiz str : cadeia de caracteres ascii terminados pelo caractere nulo (\0)  
Exemplo
```asm
.data
msg: .asciiz "Ola mundo"
x: .word 120,130,140
```
*Obs: o msg tem o endereço de memória do primeiro caractere*

### Chamadas ao sistema (system call)
São 4 tipos de solicitação:
* escrita na saída padrão
* leitura da entrada padrão
* alocação de memória
* encerramento do processo

Para executar uma chamada ao sistema, precisamos fazer o seguinte:
1. carregar no registrador `$v0` o código da chamada
2. carregar os argumentos nos registradores `$a0 a $a3` (opcional, depende da operação)
3. executar usando a instrução `syscall`
4. colher o retorno (opcional, regs $v0 e $v1)

Para representar o zero, há um registrador especial chamado `$zero`. Exemplo:  
```asm 
add $t0, $zero, $s0 # copia o valor de $s0 para $t0
```
#### Tabela de syscall
Service |	System Call Code	| Arguments |	Result
:--: | :--: | :--: | :--:
print_int |	1 |	$a0 = integer | 	
print_float |	2 |	$f12 = float |
print_double |	3 |	$f12 = double | 	
print_string |	4 |	$a0 = string | 
read_int |	5	|	| integer (in $v0)
read_float |	6	| |	float (in $f0)
read_double |	7	|	| double (in $f0)
read_string |	8 |	$a0 = buffer, $a1 = length | 	
sbrk |	9 |	$a0 = amount | address (in $v0)
exit | 10 | | |
print_character | 11 | $a0 = character |
read_character | 12 | | character (in $v0)
open | 13 | $a0 = filename, $a1 = flags, $a2 = mode | file descriptor (in $v0)	
read | 14 | $a0 = file descriptor, $a1 = buffer, $a2 = count | bytes read (in $v0)	
write | 15 | $a0 = file descriptor, $a1 = buffer, $a2 = count | bytes written (in $v0)	
close | 16 | $a0 = file descriptor	0 (in $v0) | 
exit2 | 17 | $a0 = value |


### Instruções imediatas
São variações de algumas instruções, terminando o mnemônico com 'i', que trabalha com dois registradores e uma constante, nessa ordem. Exemplo:
```asm
addi $t0, $t0, 1 # incrementa o valor de $t0 em uma unidade
```
### Pseudoinstruções
São instruções simplificadas que não pertencem à ISA e que são traduzidas pelo montador para instruções. 3 pseudoinstruções úteis:
* li reg, const : 'load immediate' copia a constante para o registrador
* move reg1, reg2 : copia o conteúdo do reg2 para o reg1
* la reg, label : 'load adress' copia o endereço de memória associado ao rótulo label para o registrador reg
### Imprimindo "Ola mundo"
```asm
.data
msg: .asciiz "Ola mundo\n"
.text
# syscall 4: imprime a string; endereço base em $a0
main: li $v0,4
la $a0,msg
syscall
# syscall 10: encerra o processo
li $v0,10
syscall
```
