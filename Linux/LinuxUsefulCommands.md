# Linux useful commands 

[#linux]

### Linux data streams
| Stream        | number | Description  |
|---------------|--------|--------------|
| stdin | 0      | It stands for standard input, and is used for taking text as an input. |
| stdout | 1      | It stands for standard output, and is used to text output of any command you type in the terminal |
| stderr | 2      | It stands for standard error |

```
$ ls -laH > myfile.txt // redirect stdout to file
$ ls -laH 1> myfile.txt // redirect stdout to file using stream number
$ ls -la ddd 2> error.txt // redirect stderr to file
$ $ psql tecmintdb < tecmintdb.sql
```

### Linux piping commands
The pipe is used to combine two or more commands
```
$ ls | grep file.txt
```

### Linux commands
format linux command: `command keys arguments`

`id` - id command in Linux is used to find out user and group names and numeric ID’s (UID or group ID) of the current user or any other user in the server. This command is useful to find out the following information as listed below:

* User name and real user id.
* Find out the specific Users UID.
* Show the UID and all groups associated with a user.
* List out all the groups a user belongs to.
* Display security context of the current user.pwd - print working directory.
* The Absolute path always starts from the root directory (/). A relative path starts from the current directory.

`ls` - (list) show list of all files and dirs. -l long format, -a show all files (inculde hidden that starts with dot .)
ls -l displays the files and folders with permission bits. _If the line starts with a -, it's a file, if it's a directory, it starts with a d_

```
$ ls -1 // display One File Per Line Using
$ ls -lah // long + hidden + human readable 
$ ls -lt // older files based on last modification time
$ ls -ltr // same as above in reverse order
```

`touch` It is used to create, change and modify the timestamps of a file. If file is not exists it creates a file without any content.
```
$ touch test
$ touch /tmp/test.txt
$ touch test1 test2 test3 test4 test5
$ touch test{1,2,3,4,5}
$ touch -a fileName // change only access time
$ touch -m fileName // change only modification time
$ touch -d "17 Mar 2023" Geek.txt
$ touch -r second_file_name first_file_name // to use the timestamp of another file
```

`rm` (remove) command is used to remove objects such as files, directories, symbolic links and so on
```
$ rm a.txt
$ rm b.txt c.txt
$ rm -i d.txt // interractive 
$ rm -f e.txt // force for write protected
$ rm -r dir // delete all the files and sub-directories recursively of the parent directory
```

`rmdir` (remove dir) useful when you want to remove the empty directories from the filesystem in Linux

```
$ rmdir mydir1 mydir2 mydir3
$ rmdir -p mydir1/mydir2/mydir3/...../mydirN // including subdirectories
```

`mkdir` (make dir) allows the user to create directories

```
$ mkdir -p first/second/third // create parent if necessary
```

`history` command is used to view the previously executed command.

```
$ history 5
$ history | grep chpasswd
$ history -d 1996 // remove command 1996
$ history -c // clean whole history
$ history | tail // last command 
```

`cat` (concatenate) It reads data from the file and gives its content as output

```
$ cat file_name
$ cat file_name1 file_name2
$ cat -n file_name // view content preceding with line number
$ cat > newfile_name // create a file and add content
$ cat filename-whose-contents-is-to-be-copied > destination-filename // copy content
$ cat -s file_name // suppress repeated empty lines
$ cat file1 >> file2 // append content
$ tac file_name // display content in reverse order
```

`lsof` command stands for (List Of Open File). This command provides a list of files that are opened. 
Basically, it gives the information to find out the files which are opened by which process.

```
$ lsof
$ lsof -u username
$ lsof -p process ID
$ lsof -p ^process ID // all other process
$ lsof -R // parent process id
$ lsof -D directory path
$ lsof -i // files opened by network connections
```