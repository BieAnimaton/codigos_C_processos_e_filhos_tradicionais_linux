
# Criando processos e filhos 
Códigos feitos em C para criar e manipular processos tradicionais e filhos tradicionais no Linux (distro usada: Slax)

# Observações adicionais:
Para compilar: cc progN.c -o progN  
Para executar: ./progN

## Comando para criar processo filho através do Gerenciador de Processos.
```
...
fork();
...
```
Caso seja colocado dentro de um laço, vai criar filhos até o computador travar (consumir toda a memória).... então, nem pensar ＞︿＜.


## Criando variável "var" do tipo "pid_t" para armazenar o valor retornado do "fork()".
```
pid_t var;
...
var = fork();
...
```
A "var" do filho copia "var" do pai, ou seja, "var" do filho é igual a 0, PORÉM, "var" do pao recebe o "PID" do processo filho e é alterado  
- **"var" do filho = 0**  
- **"var" do pai = 18463** (número escolhido aleatoriamente - sempre muda)

## Criar condição para apenas o filho executar.
```
pid_t var;
...
var = fork();
if (var == 0) {
  printf("Sou o processo filho");
  sleep(2);
  ...
}
sleep(3);
printf("Sou o processo pai");
...
```
Caso não coloquemos nenhum comando de finalização no bloco do filho, o próprio filho "escaprá" de seu bloco e executará o "printf()" de fora, ou seja, a linha do pai.  

Ao rodarmos o exemplo, a frase "Sou o processo pai" é impressa duas vezes (uma depois de 3 segundos - pelo pai - e a outra depois da finalização do programa - pelo filho -).
## Comando para finalização do processo filho.
```
pid_t var;
...
var = fork();
if (var == 0) {
  printf("Sou o processo filho");
  sleep(2);
  ...
  exit(0);
}
sleep(3);
printf("Sou o processo pai");
...
```
O comando "exit(0);" é mais aconselhado para esta tarefa.
## Imprimindo identificadores do próprio processo e do processo pai.
```
pid_t var;
...
var = fork();
if (var ==0) {
	printf("O meu PID e %d.\n",getpid());
	printf("O PID do meu PAI e %d.\n", getppid());
  ..
  exit(0);
}
sleep(3);
printf("Sou o processo pai");
...
```
- **"getpid()"**: retorna o PID (identificador) do processo  
- **"getppid()"**: retorna o PID (identificador) do pai (parent) do processo  
## Usar "sleep(N);" não é recomendado - usar "waitpid(x, y, 0);.
```
pid_t var;
int estado;
...
var = fork();
if (var ==0) {
	printf("O meu PID e %d.\n",getpid());
	printf("O PID do meu PAI e %d.\n", getppid());
  ..
  exit(0);
}
waitpid(var, estado, 0)
printf("Sou o processo pai");
...
```
O comando "waitpid()" segue a seguinte lógica: quando o "estado" de "var" for 0, encerro.  
## Variáveis não são compartilhadas.
Como estamos trabalhando com processos **TRADICIONAIS**, precisamos lembrar que o filho é uma copia por completo do pai, mas não compartilham nada entre si.  
```
pid_t filho1, filho2;
int estado, valor = 10;

filho1 = fork();
if ( filho1 == 0) {
   valor = valor + 5;
   printf("Processo-Filho 1: O valor de VALOR e %d.\n", valor);
   sleep(5);
   printf("Processo-Filho 1: Encerrando a execucao.\n");
   exit(0);
}

filho2 = fork();
if ( filho2 == 0) {
   valor = valor - 2;
	 printf("Processo-Filho 2: O valor de VALOR e %d.\n", valor);
   sleep(5);
   printf("Processo-Filho 2: Encerrando a execucao.\n");
   exit(0);
}

waitpid(filho1,estado,0);
waitpid(filho2,estado,0);

printf("Sou o processo pai");
printf("Processo-Pai: O valor de VALOR e %d.\n",valor);
...
```
No exemplo acima, como os dados não são compartilhados, ou seja, o pai e cada filho têm o seu próprio "valor", a saída na tela ficará da seguinte forma:  
- **"filho1" vai dizer que seu "valor" é 15,**  
- **"filho2" vai dizer que seu "valor" é 8 e**  
- **"pai" vai dizer que seu "valor" é 10**