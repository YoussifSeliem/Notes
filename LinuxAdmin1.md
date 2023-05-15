# Linux Admin 1

## Chapter 6

### Linux file system permissions

- Different permissions can be set for the own ing user, the owning group, and for all other users on the system that are not the user or a member of the owning group.
- user permissions override group permissions, which override other permissions.

- There are also just three categories of permissions which apply: read, write, and execute

  - `read (r)`:-
    - effect on file : Contents of the file can be read.
    - effect on directory : Contents of the directory (file names) can be listed.
  - `write (w)`:-
    - effect on file : Contents of the file can be changed.
    - effect on directory : Any file in the directory may be created or deleted.
  - `execute (x)`:-
    - effect on file : Files can be executed as commands.
    - effect on directory : Contents of the directory can be accessed (dependent on the permissions of the files in the directory).

- The -l option of the ls command will expa nd the file l isting to include both the permissions of a file and the ownership:
```
[student@desktopX -]$ ls -1 test 
-rw-rw-r--. 1 student student 0 Feb 8 17 : 36 test
```

- The command ls - 1 directory name will show the expanded listing of all of the files that reside inside the directory. To prevent the descent into the directory and see the expanded listing of the directory it self, add the -d option to Is:
```
[student@desktopX -]$ ls - ld /home 
drwxr-xr-x. 5 root root 4096 Jan 31 22 : 00 /home
```

### Changing file/directory permissions

- The command used to change permissions from the command line is `chmod`, short for "change mode" (permissions are also called the mode of a file).

- Symbolic method keywords:

  ```
  chmod WhoWhatWhich file|directory
  ```
  
  - Who is u, g, o, a (for user, group, other, all)
  - What is +, -, = (for add, remove, set exactly)
  - Which is r, w, x (for read, write, executable)

- Numeric method:

  ```
  chmod ### file|directory
  ```
  
  - the `###` is an octal number (each digit accepts value from 0 to 7) the ### represents owner, group and others permssions respectively and each # corresponds to 3 bit binary number which mapped to rwe. So if # = 4 then it's equivelent to 100 and after mapping 100 on rwe we get only r permission and w,e are disabled.

### Changing file/directory user or group ownership

- A newly created file is owned by the user who creates the file. By default, the new file has a group ownership which is the primary group of the user creating the file.

- File ownership can be changed with the `chown` command.

- `chown` can be used with the `-R` option to recursively change the ownership of an entire directory tree.

- The `chown` command can also be used to change group ownership of a file by preceding the group name with a colon `:`.

- The `chown` command can also be used to change both owner and group at the same time by using the syntax `owner: group`.

```
[root@desktopX -]# chown student foofile
[root@desktopX -]# chown -R student foodir
[root@desktopX -]# chown : admins foodir 
[root@desktopX -]# chown visitor : guests foodir 
```

### Special permissions

- The `setuid (or setgid)` permission on an executable file means that the command will run as the user (or group) of the file, not as the user that ran the command.

```
[student@desktopX - ]$ ls -1 /usr/bin/passwd 
-rwsr-xr-x. 1 root root 35504 Jul 16 2010 /usr/bin/passwd
```

- The `sticky bit` for a directory sets a special restriction on deletion of files: Only the owner of the file (and root) can delete files within the directory.

```
[student@desktopX -]$ ls - ld /tmp 
drwxrwxrwt . 39 root root 4096 Feb 8 20:52 /tmp 
```

- Lastly, `setgid` on a directory means that files created in the directory will inherit the group affiliation from the directory, rather than inheriting it from the creating user.

- Effects of special permissions on files and directories
  - `u+s (suid)`:-
    - effect on file : File executes as the user that owns the file, not the user that ran the file.
    - effect on directory : No effect.
  - `g+s (sgid)`:-
    - effect on file : File executes as the group that owns the file.
    - effect on directory : Files newly created in the directory have their group owner set to match the group owner of the directory.
  - `o+t (sticky)`:-
    - effect on file : No effect. 
    - effect on directory : Contents of the directory can be accessed (dependent on the permissions of the files in the directory).


- Setting special permissions
  - Symbolically: setuid = u+s; setgid = g+s; sticky = o+t 
  - Numerically (fourth preceding digit): setuid = 4; setgid = 2; sticky = 1 

### Default file permissions

- The default permissions for files are set by the processes that create them.

- The default permission in `file creation is 666` and in `directory creation is 777`.

- these permissions are not typically set when new files and directories are created. This is because some of the permissions are cleared by the `umask` of the shell process.

- `umask` by default = `002` and if we cleared it from removed file then it will get permission 664 and if we removed it from created directory it will get permissions 775.

- The system default umask values for Bash shell users are defined in the `/etc/profile` and `/etc/bashrc` files. Users can override the system defaults in their `.bash_profile` and `.bashrc` files.

***

## chapter 7

### Processes

- `fork` : An existing (parent) process duplicates its own add ress space (fork) to create a new (child) process structure.

- All processes are descendants of the first system process, which is `systemd` on a Red Hat Enterprise Linux 7 system.

- A child process may then `exec` its own program code.

- Normally, a parent process sleeps while the child process runs, setting a request `wait` to be signaled when the child completes.

- `zombie` : The remainder of the child process Upon exit after the child process has already closed or discarded its resources and environment.

- There are many process states : **Running**, **Sleeping**, **Stopped** and **Zombie**.
  - Running :-
    - flag (R\) -> `TASK_RUNNING`: The process is either executing on a CPU or waiting to run. Running Process can be executing user routines or kernel routines (system calls), or be queued and ready when in the Running (or Runnable) state.
  
  - Sleeping :-
    - flag (S) -> `TASK_INTERRUPTIBLE`: The process is waiting for some condition: a hardware request, system resource access or signal . When an event or signal satisfies the condition, the process returns to Running.
    - flag (D) -> `TASK_UNINTERRUPTIBLE`: This process is also Sleeping, but unlike s state, will not respond to delivered signals. Used only under specific conditions in which process i nterruption may cause an unpredictable device state.
    - flag (K) -> `TASK_KILLABLE`: Identical to the uninterruptible D state, but modified to allow the waiting task to respond to a signal to be killed (exited completely).Utilities frequently display Killable processes as D state.

  - Stopped :-
    - flag (T) -> `TASK_STOPPED`: The process has been Stopped (suspended), usually by being signaled by a user or a not her process. The process can be continued (resumed) by a not her signal to return to Running.
    - flag (T) -> `TASK_TRACED`: A process that is being debugged is a l so temporarily Stopped and shares the same T state flag.

  - Zombie :-
    - flag (Z) -> `EXIT_ZOMBIE`: A child process signals its parent as it exits. All resources except for the process identity (PiD) are released.
    - flag (X) -> `EXIT_DEAD`: When the parent cleans up (reaps) the remaining child process structure, the process is now released completely. This state will never be observed in process-listing utilities. 

- The `ps` command is used for l i sti ng current processes.
  - By default, ps with no options selects all processes with the same effective user ID (EUID) as the current user and associated with the same terminal.
  - `ps -aux` : A common display listing displays all processes, with columns in which users will be interested, and includes processes without a controlling terminal.
  - `ps -lux` : A long listing provides more technical detail, but may display faster by avoiding the username lookup.
  - `ps -ef` : to display all processes.
  - `-O` or `--sort` : sorting the rows as the default output is unsorted.

- `top` : for a repetitive update process display.

### Jobs and sessions

- `A foreground process` is a command running in a terminal window.
- `A background process` is started without a controlling terminal because it has no need for terminal interaction.
  - to run process in the back ground we appent **An ampersand (&)** to the command.
    - An ampersand will background only the last command in a pipeline, unless the command set is surrounded with parentheses.

  ```
  [student@serverx -]$ sleep 10000 & 
  [1] 5947 
  [student@serverx -]$ 
  ```

- `jobs` : tracks jobs, per session, in a table.
- `fg %job number` : bringing background job with that id to be foreground process.
- `Ctrl + z` : Suspends the job. The job is placed in the background. Pending output and keyboard typeahead are discarded.
- `Ctrl + c` : terminates the process.
- `ps j` : displays job information, including the initial command shell of each session.
- `bg %job number` : To restart the process in the background.

### Process control using signals

- A signal is a software interrupt delivered to a process. Signals report events to an executing program. Events that generate a signal can be an error, external event (e.g., i/o request or expired timer), or by explicit request (e.g., use of a signal-sending command or by keyboard sequence).

- most important signals
  - `INT` Signal Number is 2 : Causes program termination. Can be blocked or handled. Sent by typing **INTR** character **(Ctrl + c)**.
  - `KILL` Signal Number is 9 : Causes abrupt program termination. Cannot be blocked, ignored, or handled; always fatal.
  - `TERM` Signal Number is 15 **(default)** : Causes program termination. Unlike **SIGKILL**, can be blocked, ignored, or handled. The polite way to ask a program to terminate; allows self-cleanup.

- It is recommended to send SIGTERM first, then retry with SIGKILL only if a process fails to respond. 

  - `kill` command : sends a signal to a process by ID.
    - `kill PID`
    - `kill -signal PID`
    - `kill -l`
  
  - `killall` command : sends a signal to one or more processes matching selection criteria, such as a command name, processes owned by a specific user, or all system-wide processes.
  - `pkill` command : like **killall** command it uses advanced selection criteria, which can include combinations of: command, UID, GID, Parent and Terminal.

- `w` command : views users currently logged into the system and their cumulative activities. Use the TTY and FROM columns to determine the user's location.
  - `w -f` **(-f) option** : for making remote users display their connecting system name in the FROM column.

- `pstree` command : view a process tree for the system or a single user.

***

## Notes and Question

> in this section i will write the tricky questions and notes i face.

**Q1** : Update some settings to make any user created at the future has **sudo** privilages

**Answer :-**
when you write the command
```bash
[root@localhost ~]# adduser -D
```
you will get the default settings for the new user
you can change the default group for the new users to be `wheel` group because wheel group has sudo privilage
this is done by this command
```bash
[root@localhost ~]# adduser -D -g wheel
```
after this the default primary group for the new users should be 10 but this isn't enough
we need after that to open the file `/etc/login.defs`and go to the line containing `USERGROUPS_ENAB yes` and make it `no` instead of yes.

**USERGROUPS_ENAB** makes the primary group of the user to be removed by removing the user and this can't happen with important group like **wheel** so making this field's value **yes** leads to creating new group each time you create a user so it can be removed by removing the user and then the default setting we put willn't be effective in that case.

But when we turn the value to **no** removing the user will not remove its primary group so there's no need to create new group each time you create a user.

At the end the default settings will be effective and the user will get **wheel** group as primary group.