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