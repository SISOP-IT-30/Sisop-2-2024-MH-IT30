## Praktikum [Modul 1](https://github.com/lab-kcks/Modul-Sisop/tree/main/Modul-1)

Mata Kuliah Sistem Operasi

Dosen pengampu : Ir. Muchammad Husni, M.Kom.


## Kelompok Praktikum [IT-30]

- [Fico Simhanandi - 50272310](https://github.com/PuroFuro)
- [Jody Hezekiah - 5027221050](https://github.com/imnotjs)
- [Nabiel Nizar Anwari - 5027231087](https://github.com/bielnzar)



## Soal 2

Paul adalah seorang mahasiswa semester 4 yang diterima magang di perusahaan XYZ. Pada hari pertama magang, ia diberi tugas oleh atasannya untuk membuat program manajemen file sederhana. Karena kurang terbiasa dengan bahasa C dan environment Linux, ia meminta bantuan kalian untuk mengembangkan program tersebut.

**A)** Atasannya meminta agar program tersebut dapat berjalan secara daemon dan dapat mengunduh serta melakukan unzip terhadap file berikut. Atasannya juga meminta program ini dibuat tanpa menggunakan command system()

Pertama-tama kita membuat function untuk melakukan pengecekan kalau file/folder sudah ada atau tidak.
```c
int ada(const char *fname){
    char fixname[100] = "/home/purofuro/Fico/Soal_2/";
    strcat(fixname, fname);
    if (access(fixname, F_OK) == 0) {
        return 1;
    } else {    
        return 0;
    }
}
```
Function ini akan meletekan menggabungkan path dan nama dari file agar daemon bisa mengetahui dimana letak file tersebut.

Lalu membuat function yang akan mengdownload file yang tertera di link yang diberikan menggunakan daemon.

```c
int default_act(){

    int status;
    if(ada("library.zip") == 0){
        pid_t download_child = fork();
        if(download_child < 0){
            printf("garpu gagal");
            exit(EXIT_FAILURE);
        }
        else if(download_child == 0){
            backup_mode = 0;
            char *argv[6] = {"Download", "--content-disposition", "https://docs.google.com/uc?export=download&id=1rUIZmp10lXLtCIH3LAZJzRPeRks3Crup", "-P", "/home/purofuro/Fico/Soal_2", NULL};
            execv("/bin/wget", argv);
        }
        
    } 

    wait(&status);
    if(WIFEXITED(status)){
        if(ada("library") == 0){
            pid_t unzip_child = fork();
            if(unzip_child == 0){
                char *argv[5] = {"unzip", "/home/purofuro/Fico/Soal_2/library.zip", "-d", "/home/purofuro/Fico/Soal_2", NULL};
                execv("/bin/unzip", argv);
            }
            
        } else{
            pid_t lib_delete_child = fork();
            if(lib_delete_child < 0){
                printf("garpu gagal");
                exit(EXIT_FAILURE);
            }

            else if(lib_delete_child == 0){
                backup_mode = 0;
                if(ada("library.zip") != 0){
                    pid_t del_childchild = fork();
                    if(del_childchild == 0){
                        char *argv[4] = {"remove", "-rf", "/home/purofuro/Fico/Soal_2/library", NULL};
                        execv("/bin/rm", argv);
                    }
                    wait(&status);
                    if(WIFEXITED(status)){
                        pid_t zip_child = fork();
                        if(zip_child == 0){
                            char *argv[5] = {"unzip", "/home/purofuro/Fico/Soal_2/library.zip", "-d", "/home/purofuro/Fico/Soal_2", NULL};
                            execv("/bin/unzip", argv);
                        }
                        else{
                            wait(&status);
                            if(WIFEXITED(status)){
                                rename_stuff();
                            }
                        }
                    }
                }
            }
        }
    }
}
```
Inti atau secara simpelnya, kode diatas melakukan pengecekan terlebih dahulu, apakah file/folder tersebut ada atau tidak, dan bila ada maka file tersebut akan didownload dan bila tidak ada maka akan dihapus.

Bagian awal dari `./management` saat di-run

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management.png)

Proses "delete" dan "Rename"

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management_proses.png)

Proses `./management` selesai

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management_done.png)

**B)** Setelah ditelusuri, ternyata hanya 6 file teratas yang nama filenya tidak dienkripsi. Oleh karena itu, bantulah Paul untuk melakukan dekripsi terhadap nama file ke-7 hingga terakhir menggunakan algoritma ROT19.

Disini mungkin code terlihat sangat panjang tetapi yang dilakukan oleh code ini hanyalah pertama-tama adalah membuka directory dan mengecek apakah huruf pertama dari file merupakan angka atau bukan.

```c
int rename_stuff(){
    char *basePath = "/home/purofuro/Fico/Soal_2/library";
    struct dirent *dp;
    DIR *dir = opendir(basePath);

    if (!dir)
        return 0;

    while ((dp = readdir(dir)) != NULL)
    {   
        
        if(default_mode == 1){
            if (strcmp(dp->d_name, ".") != 0 && strcmp(dp->d_name, "..") != 0){

                char to_change[1000] = "/home/purofuro/Fico/Soal_2/library/";
                char original_name[1000] = "/home/purofuro/Fico/Soal_2/library/";
                char text[100], ch;
                int key = 19;
                strcpy(text, dp->d_name);
                if(text[0] >= '0' && text[0] <= '9'){

                    if(strstr(dp->d_name, "d3Let3") != NULL){
                        strcat(to_change, dp->d_name);
                        remove(to_change);
                        timelog(text, "delete");

                    } else if(strstr(text, "r3N4mE") != NULL){
                        //rename the files
                        strcat(to_change, dp->d_name);

                        if(strstr(text, ".ts") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/helper.ts");
                            timelog("helper.ts", "rename");
                        }
                        else if(strstr(text, ".py") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/calculator.py");
                            timelog("calculator.py", "rename");
                        }
                        else if(strstr(text, ".go") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/server.go");
                            timelog("server.go", "rename");
                        }
                        else{
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/renamed.file");
                            timelog("renamed.file", "rename");
                        }
                    }
```

Apabila bukan merupakan angka, maka code akan menjalankan decrypt sekaligus menjalankan permintaan di bagian **C)** yang berbunyi.

Setelah dekripsi selesai, akan terlihat bahwa setiap file memuat salah satu dari kode berikut: r3N4mE, d3Let3, dan m0V3. Untuk setiap file dengan nama yang memuat kode d3Let3, hapus file tersebut. Sementara itu, untuk setiap file dengan nama yang memuat kode r3N4mE, lakukan hal berikut:
- Jika ekstensi file adalah “.ts”, rename filenya menjadi “helper.ts”
- Jika ekstensi file adalah “.py”, rename filenya menjadi “calculator.py”
- Jika ekstensi file adalah “.go”, rename filenya menjadi “server.go”
- Jika file tidak memuat salah satu dari ekstensi diatas, rename filenya menjadi “renamed.file”


```c
                } else{
                    for (int i = 0; text[i] != '\0'; ++i) {

                        ch = text[i];
                        // Check for valid characters.
                        if (isalnum(ch)) {
                            //Lowercase characters.
                            if (islower(ch)) {
                                ch = (ch - 'a' - key + 26) % 26 + 'a';
                            }
                            // Uppercase characters.
                            if (isupper(ch)) {
                                ch = (ch - 'A' - key + 26) % 26 + 'A';
                            }
                            // Numbers.
                        }
                        // Adding decoded character back.
                        text[i] = ch;

                    }
                    if(strstr(text, "d3Let3") != NULL){
                        strcat(to_change, dp->d_name);
                        remove(to_change);
                        timelog(dp->d_name, "delete");

                    } else if(strstr(text, "r3N4mE") != NULL){
                        //rename the files
                        strcat(to_change, dp->d_name);
                        if(strstr(text, ".ts") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/helper.ts");
                            timelog("helper.ts", "rename");
                        }
                        else if(strstr(text, ".py") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/calculator.py");
                            timelog("calculator.py", "rename");
                        }
                        else if(strstr(text, ".go") != NULL){
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/server.go");
                            timelog("server.go", "rename");
                        }
                        else{
                            rename(to_change, "/home/purofuro/Fico/Soal_2/library/renamed.file");
                            timelog("renamed.file", "rename");
                        }

                    } else if(strstr(text, "m0V3") != NULL){
                        strcat(original_name, dp->d_name);
                        strcat(to_change, text);
                        rename(original_name, to_change);
                        timelog(dp->d_name, "rename");
                    }
                }  
            }
        }
        sleep(1);
    }
    closedir(dir);
}
```

Hal ini dilakukan dengan menggunakan `strstr` untuk mengecek apakah string memiliki kata tersebut dan melakukan aksi sesuai dengan apa yang diinginkan dan algoritman untuk aksi sesuai dengan nama ini juga dilakukan kepada 6 file pertama tadi.

**D)** Atasan Paul juga meminta agar program ini dapat membackup dan merestore file. Oleh karena itu, bantulah Paul untuk membuat program ini menjadi 3 mode, yaitu:
- default: program berjalan seperti biasa untuk me-rename dan menghapus file. Mode ini dieksekusi ketika program dijalankan tanpa argumen tambahan, yaitu dengan command ./management saja
- backup: program memindahkan file dengan kode m0V3 ke sebuah folder bernama “backup”
- restore: program mengembalikan file dengan kode m0V3 ke folder sebelum file tersebut dipindahkan
`Contoh penggunaan: ./management -m backup`


Hal ini dilakukan dengan membuat `int main` memiliki opsi untuk diberikan argumen

```c
int main(int argc, char *argv[]){

    signal(SIGRTMIN,signal_mode);
    signal(SIGUSR1, signal_mode);
    signal(SIGUSR2, signal_mode);
    signal(SIGTERM, signal_mode);
    pid_t pid, sid;        // Variabel untuk menyimpan PID

    pid = fork();     // Menyimpan PID dari Child Process

    /* Keluar saat fork gagal
    * (nilai variabel pid < 0) */
    if (pid < 0) {
    exit(EXIT_FAILURE);
    }

    /* Keluar saat fork berhasil
    * (nilai variabel pid adalah PID dari child process) */
    if (pid > 0) {
    exit(EXIT_SUCCESS);
    }

    umask(0);

    sid = setsid();
    if (sid < 0) {
    exit(EXIT_FAILURE);
    }

    if ((chdir("/")) < 0) {
    exit(EXIT_FAILURE);
    }
    printf("The PID %d\n", getpid());

    close(STDIN_FILENO);
    close(STDOUT_FILENO);
    close(STDERR_FILENO);

    while(1){
        //check
        if(argc == 1){
            if(default_mode == 1) default_act();
        }
        if(argc >= 2){
            if(strcmp(argv[1], "-m") == 0){
                if(backup_mode == 0){
                    if(strcmp(argv[2], "backup") == 0){
                        backup_act();
                    }
                }
```

Dimana jika `./management -m backup` diketik, maka fungsi `backup_act` akan dipanggil yang dimana isi dari fungsi tersebut adalah.

```c
int backup_act(){

    int status;
    char *basePath = "/home/purofuro/Fico/Soal_2/library";
    struct dirent *dp;
    DIR *dir = opendir(basePath);

    if (!dir) return 0;

    while ((dp = readdir(dir)) != NULL){
        
        char filename[1000] = "/home/purofuro/Fico/Soal_2/library/";
        if(backup_mode == 1){
            if (strcmp(dp->d_name, ".") != 0 && strcmp(dp->d_name, "..") != 0){
            
                if(strstr(dp->d_name, "m0V3") != NULL){
                    strcat(filename, dp->d_name);
                    pid_t multi_child = fork();
                    if(multi_child == 0){
                        char *argv[4] = {"backing up", filename, "/home/purofuro/Fico/Soal_2/library/backup", NULL}; 
                        execv("/bin/mv", argv);
                    }
                }
                timelog(dp->d_name, "backup");
                
            }
            sleep(1);
        }
    }
    closedir(dir);
}
```

Jika kita lihat, konsep penggunaannya sama dengan `default_act` hanya saja, untuk memindahkannya menggunakan `execv` yang memuat aksi `mv` kedalam folder backup yang sudah dibuat disaat melakukan `default_act` tadi.


```c
                else if(strcmp(argv[2], "restore") == 0){
                    if(restore_mode){
                        restore_act();
                    }
                }
            }
        }
        sleep(1000);
    }
    return 0;
}
```

Apabila yang diketik adalah `./management -m restore` maka fungsi `restore_act` akan dipanggil dan fungsinya sendiri berisi hal berikut.

```c
int restore_act(){

    int status;
    char *basePath = "/home/purofuro/Fico/Soal_2/library/backup";
    struct dirent *dp;
    DIR *dir = opendir(basePath);

    if (!dir) return 0;

    while ((dp = readdir(dir)) != NULL){

        
        char filename[1000] = "/home/purofuro/Fico/Soal_2/library/backup/";
        if(restore_mode == 1){
            if (strcmp(dp->d_name, ".") != 0 && strcmp(dp->d_name, "..") != 0){
                
                if(strstr(dp->d_name, "m0V3") != NULL){
                    strcat(filename, dp->d_name);
                    pid_t multi_child = fork();
                    if(multi_child == 0){
                        char *argv[4] = {"backing up", filename, "/home/purofuro/Fico/Soal_2/library", NULL}; 
                        execv("/bin/mv", argv);
                    }
                }
                timelog(dp->d_name, "restore");
            
            }  
            sleep(1);
        }
    }
    closedir(dir);
}
```

Isinya sendiri sama seperti yang ada di `backup` hanya saja ia meletakkan file dari folder *backup* ke folder awal yaitu *library*.

**E)** Terkadang, Paul perlu mengganti mode dari program ini tanpa menghentikannya terlebih dahulu. Oleh karena itu, bantulan Paul untuk mengintegrasikan kemampuan untuk mengganti mode ini dengan mengirim sinyal ke daemon, dengan ketentuan:
- SIGRTMIN untuk mode default
- SIGUSR1 untuk mode backup
- SIGUSR2 untuk mode restore
`Contoh penggunaan: kill -SIGUSR2 <pid_program>`

Untuk soal ini, telah kita lihat bahwa tadi di `int main` terdapat beberapa kode yang memanggil fungsi `signal_mode`, dimana fungsi itu sendiri berisi dengan

```c
void signal_mode(int signal){
    if(signal == SIGRTMIN){
        write(STDOUT_FILENO, "Default chosen", 13);
        default_mode = 1;
        restore_mode = 0;
        backup_mode = 0;
       
    } else if(signal == SIGUSR1){
        write(STDOUT_FILENO, "Backup chosen", 13);
        backup_mode = 1;
        default_mode = 0;
        restore_mode = 0;
        backup_act();

    
    } else if(signal == SIGUSR2){
        write(STDOUT_FILENO, "Restore chosen", 13);
        restore_mode = 1;
        backup_mode = 0;
        default_mode = 0;
        restore_act();


    } else if(signal == SIGTERM){
        exit(EXIT_SUCCESS);
    }
}
```

Disini terlihat bahwa terdapat semacam switch yang bila signal tersebut dipanggil maka ia akan menyalakan switchnya sendiri dan mematikan switch dari variabel yang lain. Setelah itu akan dipanggil fungsi yang sesuai dengan signal mana yang di-kill. Untuk variabel switchnya sendiri ada dibawah kode-kode `#include` yang memuat isi.

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <unistd.h>
#include <syslog.h>
#include <string.h>
#include <sys/wait.h>
#include <dirent.h>
#include <ctype.h>
#include <signal.h>
#include <time.h>

int default_mode = 1;
int backup_mode = 1;
int restore_mode = 1; 
int exiting = 0; 
```

Menjalankan `./management -m backup` via `kill -USR1 PID`

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/kill_usr1.png)

Menjalankan `./management -m restore` via `kill -USR2 PID`

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/restore.png)

**F)** Program yang telah dibuat ini tidak mungkin akan dijalankan secara terus-menerus karena akan membebani sistem. Maka dari itu, bantulah Paul untuk membuat program ini dapat dimatikan dengan aman dan efisien.

```c
    } else if(signal == SIGTERM){
        exit(EXIT_SUCCESS);
    }
}
```

Sebelum dan setelah mematikan daemon menggunakan via `kill -TERM PID`

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management_show.png)

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management_gone.png)

Untuk bagian ini, terlihat bahwa di `signal_mode` terdapat SIGTERM yang bila dipanggil maka akan mengeluarkan daemon secara aman dengan `exit(EXIT_SUCCESS)`

**G)** Terakhir, program ini harus berjalan setiap detik dan mampu mencatat setiap peristiwa yang terjadi ke dalam file .log yang bernama “history.log” dengan ketentuan:
- Format: [nama_user][HH:MM:SS] - <nama_file> - <action>
- nama_user adalah username yang melakukan action terhadap file
- Format action untuk setiap kode:
    - kode r3N4mE: Successfully renamed.
    - kode d3Let3: Successfully deleted.
    - mode backup: Successfully moved to backup.
    - mode restore: Successfully restored from backup.
Contoh pesan log:
- `[paul][00:00:00] - r3N4mE.ts - Successfully renamed.`
- `[paul][00:00:00] - m0V3.xk1 - Successfully restored from backup.`

Apabila kita melihat ke semua fungsi kita sebelumnya contohnya `default_act`, maka akan terlihat bahwa di akhir fungsi terdapat pemanggilan fungsi yaang bernama `timeog`, dimana `timelog` itu tersebut berisi dengan.

```c
void timelog(char *name, char *action){

    char result[1000];
    time_t my_time;
    struct tm * timeinfo; 
    time (&my_time);
    timeinfo = localtime (&my_time);

    if(strstr(action, "restore") != NULL ){
        sprintf(result, "[PuroFuro][%d:%d:%d] - %s - Succesfully restored from backup.", timeinfo->tm_hour, timeinfo->tm_min, timeinfo->tm_sec, name);

    } else if(strstr(action, "backup") != NULL ){
        sprintf(result, "[PuroFuro][%d:%d:%d] - %s - Succesfully moved to backup.", timeinfo->tm_hour, timeinfo->tm_min, timeinfo->tm_sec, name);

    
    } else if(strstr(action, "delete") != NULL ){
        sprintf(result, "[PuroFuro][%d:%d:%d] - %s - Succesfully Deleted.", timeinfo->tm_hour, timeinfo->tm_min, timeinfo->tm_sec, name);

    
    } else if(strstr(action, "rename") != NULL ){
        sprintf(result, "[PuroFuro][%d:%d:%d] - %s - Succesfully Renamed.", timeinfo->tm_hour, timeinfo->tm_min, timeinfo->tm_sec, name);

    }
    FILE *file = fopen("/home/purofuro/Fico/Soal_2/history.log", "a"); 
    if (file != NULL) {  
        fprintf(file, "%s\n", result); 
        fclose(file); 
    } else { 
        printf("Error opening the file\n"); 
    } 
}
```

Menunjukan isi dari history.log

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/management_history.png)

Disini terdapat if else yang akan mengecek sesuai dari argumen yang diberikan saat fungsi ini berjalan dan akan melakukan aksi sesuai dari if else mana yang bekerja. Kode ini menggunakan `sprintf` itu menggabungkan string-string yang ada seperti contohnya yang berasal dari `timeinfo`.

**H)** Berikut adalah struktur folder untuk pengerjaan nomor 2:
```   
    soal_2/
    ├── history.log
    ├── management.c
    └── library/
        └── backup/
```

## Hasil akhir repository

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/repo1.png)

![github-small](https://github.com/PuroFuro/image_for_sisop/blob/main/repo2.png)
