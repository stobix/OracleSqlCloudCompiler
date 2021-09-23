# OracleSqlCloudCompiler
An expect script to compile local plsql packages into an oracle cloud db

Uses a locally installed sqlPlus installation to connect to a db, compile the package and format the compile output in a meaningful way.

Prerequisites: 
* Have sqlplus installed locally *TODO Insert link to oracle's page here*
* Have an extracted and modified wallet file that lets you log in to cloud via sqlplus *TODO Insert link to oracle's page here*
* Have `expect` installed


For now, call the script as 
```
oscc user@db pw extracted_wallet_path file_to_compile.pkb
```

Or, preferably, create a file `sqlcompile` with your credentials and make it executable:
```
#!/usr/bin/bash
oscc user@mydb mySecretPass /path/to/file $1
```


Relevant snippet from my ~/.vimrc:
```
aug orcl
    au BufRead,BufNewFile *.pkb,*.pks setl ft=plsql " set the file type to plsql
    au BufRead,BufNewFile *.pkb,*.pks setl errorformat=%f\ %l/%c:%m " parse the output from oscc correcly
    au BufRead,BufNewFile *.pkb,*.pks setl makeprg=make\ %< " Compile only the current file - We don' want all packages to change on the server just 'cause we update one thing!
aug end
```
My project Makefile: 
```
SRC=$(wildcard *.pks *.pkb)

all: $(patsubst %.pkb,%,$(SRC))

%.pkb : %.pks
	sqlcompile $<

% : %.pkb
	sqlcompile $<
```

