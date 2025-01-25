```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <sys/stat.h>
#include <dirent.h>
#include <fcntl.h>
#include <string.h>

void display_directory(const char *path) {
    DIR *dir = opendir(path);
    if (dir == NULL) {
        perror("opendir");
        exit(EXIT_FAILURE);
    }

    printf("\nContents of directory: %s\n", path);
    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        printf("%s\n", entry->d_name);
    }

    closedir(dir);
}

void display_file_info(const char *filename) {
    struct stat file_stat;
    if (stat(filename, &file_stat) == -1) {
        perror("stat");
        exit(EXIT_FAILURE);
    }

    printf("\nFile information for: %s\n", filename);
    printf("File size: %ld bytes\n", file_stat.st_size);
    printf("File permissions: %o\n", file_stat.st_mode & 0777);
    printf("Last modified: %ld\n", file_stat.st_mtime);
}

int main() {
    pid_t pid;
    int status;

    printf("Parent process ID: %d\n", getpid());

    pid = fork();

    if (pid < 0) {
        perror("fork");
        exit(EXIT_FAILURE);
    }

    if (pid == 0) { // Child process
        printf("Child process ID: %d\n", getpid());

        // Use exec to run another program (e.g., "ls")
        printf("Executing 'ls -l' in child process:\n");
        execl("/bin/ls", "ls", "-l", NULL);

        // If exec fails
        perror("exec");
        exit(EXIT_FAILURE);
    } else { // Parent process
        printf("Parent waiting for child to finish...\n");
        wait(&status);

        if (WIFEXITED(status)) {
            printf("Child exited with status: %d\n", WEXITSTATUS(status));
        }

        // Demonstrate stat, opendir, and readdir
        const char *directory = ".";
        const char *filename = "example.txt";

        // Display directory contents
        display_directory(directory);

        // Display file information
        display_file_info(filename);

        // Close a dummy file descriptor to demonstrate close()
        int fd = open(filename, O_RDONLY | O_CREAT, 0644);
        if (fd == -1) {
            perror("open");
            exit(EXIT_FAILURE);
        }

        printf("Closing file descriptor: %d\n", fd);
        if (close(fd) == -1) {
            perror("close");
            exit(EXIT_FAILURE);
        }
    }

    printf("Parent process exiting.\n");
    return 0;
}
```
