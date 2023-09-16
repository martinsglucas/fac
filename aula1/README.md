### Arquitera de um computador
Compreende um projeto estrutural de um computador, ou seja, componentes lógicos que determinam seu funcionamento  
Um sistema computacional atualmente compreende três camadas:
* **Camada externa**: Software de aplicação
* **Camada intermediária**: Software de sistemas
* **Camada interna**: Hardware

![texto](img.png)  


```asm
.data
msg: .asciiz "Ola mundo\n"
.text
main: li $v0,4
la $a0,msg
syscall
li $v0,10
syscall
```
