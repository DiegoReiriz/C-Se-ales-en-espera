#include <stdio.h>
#include <stdlib.h>
#include <signal.h>

static void handler(int sigNumber){
	
	switch(sigNumber){
		case SIGUSR1:
			printf("Recibido SIGUSR1\n");
			break;
		case SIGUSR2:
			printf("Recibido SIGUSR2\n");
			break;
		default:
			
			break;
	}
	
}

int main(){
	int status;
	pid_t padre,h1,h2;
	
	sigset_t mask;
	struct sigaction sig;
	
	padre=getpid();

	sig.sa_handler = &handler;	
	sig.sa_flags = SA_RESTART;
	
	if(sigaction(SIGUSR1,&sig,NULL) == -1)	
		printf("Error ao asignar o manejador de señales\n");
	
	if(sigaction(SIGUSR2,&sig,NULL) == -1)	
		printf("Error ao asignar o manejador de señales\n");

	sig.sa_handler=SIG_IGN;
	if(sigaction(SIGINT,&sig,NULL) == -1)	
		printf("Error ao asignar o manejador de señales\n");
		
	sigemptyset(&mask); //valores por defecto da mascara
	sigaddset(&mask,SIGUSR1); //añado a señal que quero modificar a mascara
	sigprocmask(SIG_BLOCK, &mask, NULL); //bloqueo as señales contidas pola mascara
		
	if((h1=fork()) == -1){
		printf("Error ao crear o fillo 1\n");
	}else if(h1 == 0){ // fillo 1
	
		kill(padre,SIGUSR1);
		sleep(4);
		kill(padre,SIGUSR2);
		sleep(2);
		
		exit(1);
	}else{ //pai

		if((h2=fork()) == -1){
			printf("Error ao crear o fillo 2\n");
		}else if(h2 == 0){ // fillo 2
			kill(padre,SIGINT);
			exit(2);
		}else{ //pai
			sleep(8);
			
			if(sigpending(&mask) == -1)
				printf("Error ao consultar aas sinais pendentes\n");
			else
				if(sigismember(&mask,SIGUSR1))
					printf("A sinal SIGUSR1 esta a espera\n");
			
			sigprocmask(SIG_UNBLOCK, &mask, NULL);
			
			waitpid(h1,&status,NULL);
			printf("Valor devolto polo fillo 1: %d\n",WEXITSTATUS(status));
			
			wait(&status);//recollo o fillo2
		}	
	}
		
	return EXIT_SUCCESS;
}
