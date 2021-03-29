#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <stdbool.h> 
#include <ctype.h>
#include <limits.h>
#include <unistd.h> 
#include <sys/wait.h>
#include <errno.h>


void parse(char* s) {
  const char break_chars[] = " \t";
  char* p;
  p = strtok(s, break_chars);
  while (p != NULL) {
    printf("token was: %s\n", p);
    p = strtok(NULL, break_chars);
  }
}


int main(int argc, const char * argv[]) {  
  char input[BUFSIZ];
  char last_command[BUFSIZ];
  
  memset(input, 0, BUFSIZ * sizeof(char));
  memset(input, 0, BUFSIZ * sizeof(char));
  bool finished = false;
  
////////
int fd;
int pid = fork();
int current_out;

if ((pid = fork()) < 0) 
        ...error...
else if (pid == 0)
{
    /* Be childish */
    if (in)
    {
        int fd0 = open(input, O_RDONLY);
        dup2(fd0, STDIN_FILENO);
        close(fd0);
    }

    if (out)
    {
        int fd1 = creat(output , 0644) ;
        dup2(fd1, STDOUT_FILENO);
        close(fd1);
    }
    ...now the child has stdin coming from the input file, 
    ...stdout going to the output file, and no extra files open.
    ...it is safe to execute the command to be executed.
    execve(cmd[0], cmd, env);   // Or your preferred alternative
    fprintf(stderr, "Failed to exec %s\n", cmd[0]);
    exit(1);
}
else
{
    /* Be parental */
    ...wait for child to die, etc...
}



  while (!finished) {
    printf("osh> ");
    fflush(stdout);

    if ((fgets(input, BUFSIZ, stdin)) == NULL) 
    {   // or gets(input, BUFSIZ);
      fprintf(stderr, "no command entered\n");
      exit(1);
    }
    input[strlen(input) - 1] = '\0';          // wipe out newline at end of string
    printf("input was: \n'%s'\n", input);

    // check for history (!!) command
    if (strncmp(input, "!!", 2) == 0) {
      if (strlen(last_command) == 0) {
        fprintf(stderr, "no last command to execute\n");
      }
      printf("last command was: %s\n", last_command);
    } else if (strncmp(input, "exit", 4) == 0) {   // only compare first 4 letters
      finished = true;
    } else {
      strcpy(last_command, input);
      printf("You entered: %s\n", input);   // you will call fork/exec
      parse(input);
			printf("after parse, what is input: %s\n", input);
      printf("\n");
    }
    
    int fd[2];
	pid_t pid1, pid2;
	char *argv[5];

	pipe(fd);

	/* Create the first child and run the first command. */
	pid1 = fork();
	if (pid1<0) {
		perror("First fork() failed!");
		return -1;
	}
	if (pid1==0) {
		/* Set the process output to the input of the pipe. */
		close(1);
		dup(fd[1]);
		close(fd[0]);
		close(fd[1]);
		argv[0] = (char*) malloc(5*sizeof(char));
		argv[1] = (char*) malloc(5*sizeof(char));
		strcpy(argv[0],"ls");
		strcpy(argv[1],"-l");
		argv[2] = NULL;
		fprintf(stderr,"************* Running ls -l *************\n");	
		execvp(argv[0],argv);
		perror("First execvp() failed");
		return -1;
	}

	/* Create the second child and run the second command. */
	pid2 = fork();
	if (pid2<0) {
		perror("Second fork() failed!");
		return -1;
	}
	if (pid2==0) {
		/* Set the process input to the output of the pipe. */
		close(0);
		dup(fd[0]);
		close(fd[0]);
		close(fd[1]);
		argv[0] = (char*) malloc(5*sizeof(char));
		argv[1] = (char*) malloc(5*sizeof(char));
		strcpy(argv[0],"grep");
		strcpy(argv[1],"pipe");
		argv[2] = NULL;
		fprintf(stderr,"************* Running grep pipe *************\n");
		execvp(argv[0],argv);
		perror("Second execvp() failed");
		return -1;
	}

	close(fd[0]);
	close(fd[1]);
	/* Wait for the children to finish, then exit. */
	waitpid(pid1,NULL,0);
	waitpid(pid2,NULL,0);
	printf("************* Father exitting... *************\n");
	return 0;
}

 
  
  printf("osh exited\n");
  printf("program finished\n");
  
  return 0;
}


//RESOURCES WASN'T ABLE TO ACCCESS OTHER RESOURCES BECAUSE OF PROBELMS IN SSH REMOTE

//https://en.cppreference.com/w/c/types/boolean
//https://stackoverflow.com/questions/22289428/making-a-simple-history-function-in-c-shell-program
//https://stackoverflow.com/questions/11515399/implementing-shell-in-c-and-need-help-handling-input-output-redirection
//https://stackoverflow.com/questions/13643278/library-that-has-reference-to-fork-in-c
///https://www.tutorialspoint.com/cprogramming/c_error_handling.htm
//https://stackoverflow.com/questions/9612315/why-does-my-compiler-not-accept-fork-despite-my-inclusion-of-unistd-h
//https://stackoverflow.com/questions/6542491/how-to-create-two-processes-from-a-single-parent
//https://docstore.mik.ua/orelly/unix/upt/ch13_01.htm
//https://stackoverflow.com/questions/21458166/how-to-pass-an-input-and-retrieve-an-output-to-a-child-process-in-c-language
//https://www.geeksforgeeks.org/fork-system-call/
//http://alumni.cs.ucr.edu/~drougas/code-samples-tutorials/pipe-redirection.c

