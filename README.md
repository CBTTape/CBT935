# CBT935
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 935 is from John McKown and is a port of SQLITE 3.8.11    *   FILE 935
//*           to z/OS.  The current state of this package is        *   FILE 935
//*           described below.  This is version 3.8.11 of SQLITE.   *   FILE 935
//*           An older version of this port can be found on CBT     *   FILE 935
//*           File 923 (version 3.8.7) and an even older version    *   FILE 935
//*           is found on File 897 (version 3.8).                   *   FILE 935
//*                                                                 *   FILE 935
//*           email:  john.archie.mckown@gmail.com                  *   FILE 935
//*                                                                 *   FILE 935
//*           Please look at the description below:                 *   FILE 935
//*                                                                 *   FILE 935
//*                   SQLITE 3.8.11 for z/OS                        *   FILE 935
//*                   =====================                         *   FILE 935
//*                                                                 *   FILE 935
//*     Sqlite is a self-contained, server-less,                    *   FILE 935
//*     zero-configuration, transactional SQL database engine.      *   FILE 935
//*     This is the standard sqlite library which is available      *   FILE 935
//*     on many UNIX and Linux systems. The code was compiled       *   FILE 935
//*     with almost no changes. The code is dependent on z/OS       *   FILE 935
//*     UNIX System Services.  The original code supports EBCDIC    *   FILE 935
//*     in addition to the normal ASCII. This distribution has      *   FILE 935
//*     been compiled to support EBCDIC characters and IEEE         *   FILE 935
//*     (BFP) floating point numbers. This latter is important      *   FILE 935
//*     because most other z/OS languages use the historical HFP    *   FILE 935
//*     floating point.                                             *   FILE 935
//*                                                                 *   FILE 935
//*     At present, the code only has C language bindings on        *   FILE 935
//*     z/OS.  There is a "shim" or "stub" HLASM interface          *   FILE 935
//*     program which can be called from COBOL or PL/I to access    *   FILE 935
//*     the C subroutines. It is named SQLITE3A.  The parameters    *   FILE 935
//*     passed into this routine are the same ones as documented    *   FILE 935
//*     in the sqlite C API, except that the first parameter is     *   FILE 935
//*     a C style null-delimited character string which is the      *   FILE 935
//*     name of the C subroutine to be invoked. In COBOL, this      *   FILE 935
//*     is a Z'...' character string. The COBSQLTE and PLISQLTE     *   FILE 935
//*     members are the COBOL COPY book and PL/I %INCLUDE           *   FILE 935
//*     members which define COBOL or PL/I variables which          *   FILE 935
//*     contain these strings. In the case of COBOL, the            *   FILE 935
//*     underscore characters shown in the C API names are          *   FILE 935
//*     replace by a dash because an underscore is not valid in     *   FILE 935
//*     a COBOL data name. The variable names are in upper case,    *   FILE 935
//*     but the values are in lower case, due to the fact that      *   FILE 935
//*     the C subroutine names are in lower case.                   *   FILE 935
//*                                                                 *   FILE 935
//*     Sqlite 3.8.11 is documented at http://sqlite.org, and       *   FILE 935
//*     this code runs as described there. Therefore, no futher     *   FILE 935
//*     documentation is supplied at present.  When the COBOL       *   FILE 935
//*     and PL/I bindings are done, those will be documented        *   FILE 935
//*     here.                                                       *   FILE 935
//*                                                                 *   FILE 935
//*     This document does _not_ attempt to teach you how to use    *   FILE 935
//*     sqlite. It assumes you already know how to use it, or       *   FILE 935
//*     can learn it yourself. It does attempt to explain how to    *   FILE 935
//*     use it on z/OS by showing some COBOL and PL/I examples.     *   FILE 935
//*     If you have a C license, I strongly suggest writing your    *   FILE 935
//*     code in C. It will be more understandable to most.          *   FILE 935
//*                                                                 *   FILE 935
//*     Wish list items.                                            *   FILE 935
//*     ----------------                                            *   FILE 935
//*     1. At present, the file which contains the sqlite           *   FILE 935
//*        database must reside in a UNIX subdirectory. This        *   FILE 935
//*        means that the user of sqlite must have an z/OS UNIX     *   FILE 935
//*        identity.  I would like to be able to use a VSAM         *   FILE 935
//*        Linear Dataset for storing the sqlite data at some       *   FILE 935
//*        time. Mainly due to the number of shops which have       *   FILE 935
//*        not really embraced z/OS UNIX.  This may be possible     *   FILE 935
//*        using a "shim" sqlite VFS as documented here:            *   FILE 935
//*        http://sqlite.org/vfs.html which would use a VSAM LDS    *   FILE 935
//*        as the backing store for a sqlite in-memory data         *   FILE 935
//*        base.                                                    *   FILE 935
//*                                                                 *   FILE 935
//*     2. Write a REXX interface.                                  *   FILE 935
//*                                                                 *   FILE 935
//*     3. Write a batch program based on the sqlite3 UNIX          *   FILE 935
//*        command. This would allow SQL commands to be run in a    *   FILE 935
//*        step in a batch job.                                     *   FILE 935
//*                                                                 *   FILE 935
//*     4. JDBC for Java access.                                    *   FILE 935
//*                                                                 *   FILE 935
//*                                                                 *   FILE 935
//*     Members in this library:                                    *   FILE 935
//*     ------------------------                                    *   FILE 935
//*     - $README  - This member. The README in markdown format.    *   FILE 935
//*                                                                 *   FILE 935
//*     - ASMACL   - The JCL to compile and link the SQLITE3A       *   FILE 935
//*                  assembler program.  It would be wise to run    *   FILE 935
//*                  this to recompile SQLITE3A on your system,     *   FILE 935
//*                  if at all possible.                            *   FILE 935
//*                                                                 *   FILE 935
//*     - COBSQLTE - The COBOL copy book which defines various      *   FILE 935
//*                  SQLITE3 related variables and initializes      *   FILE 935
//*                  them.  The member contains a fair number of    *   FILE 935
//*                  comments on how to use SQLITE3A in a COBOL     *   FILE 935
//*                  program to invoke each of the 204(!)           *   FILE 935
//*                  different sqlite subroutines.                  *   FILE 935
//*                                                                 *   FILE 935
//*     - COBTEST1 - The JCL to run the TESTCOB1 program.           *   FILE 935
//*                  Unless you are running z/OS 2.1, you need      *   FILE 935
//*                  to recompile the program before running        *   FILE 935
//*                  this test job. There are LE dependencies.      *   FILE 935
//*                                                                 *   FILE 935
//*     - COBTEST2 - The JCL to run the TESTCOB2 program.           *   FILE 935
//*                  Unless you are running z/OS 2.1, you need      *   FILE 935
//*                  to recompile the program before running        *   FILE 935
//*                  this test job. There are LE dependencies.      *   FILE 935
//*                                                                 *   FILE 935
//*     - IGYWCL   - The JCL to compile and link the TESTCOB1       *   FILE 935
//*                  and TESTCOB2 program. You really need to       *   FILE 935
//*                  recompile these program on your system due     *   FILE 935
//*                  to possible LE dependencies.                   *   FILE 935
//*                                                                 *   FILE 935
//*     - LINK     - The JCL to link the SQLITE3 object code        *   FILE 935
//*                  into a LINKLIB.  This composite links in       *   FILE 935
//*                  the C and LE library subroutines.              *   FILE 935
//*                                                                 *   FILE 935
//*     - LINKLIB  - XMIT copy of the SQL3811.LINKLIB library       *   FILE 935
//*                  containing the z/OS batch executable           *   FILE 935
//*                  programs: SQLITE3A, TESTCOB1, and TESTCOB2.    *   FILE 935
//*                  However, these program will likely only run    *   FILE 935
//*                  on a z/OS 2.1 system due to COBOL              *   FILE 935
//*                  dependencies on LE.                            *   FILE 935
//*                                                                 *   FILE 935
//*     - PAXFULL  - This is a compressed pax archive for the       *   FILE 935
//*                  entire SQLITE3 application, Including all      *   FILE 935
//*                  source code and make information.              *   FILE 935
//*                                                                 *   FILE 935
//*     - PLICB    - The JCL to compile a PL/I program.             *   FILE 935
//*                                                                 *   FILE 935
//*     - PLISQLTE - The PL/I %INCLUDE member to define and         *   FILE 935
//*                  initialize a number of variables to ease       *   FILE 935
//*                  the use of SQLITE3A.                           *   FILE 935
//*                                                                 *   FILE 935
//*     - SQLITE3A - LE enabled HLASM subroutine which presents     *   FILE 935
//*                  an API to the SQLITE3 C subroutines which      *   FILE 935
//*                  is designed for use by COBOL or PL/I code.     *   FILE 935
//*                  It is composite (statically) bound with the    *   FILE 935
//*                  C object code.  This code has not been         *   FILE 935
//*                  fully tested yet and may contain errors.       *   FILE 935
//*                                                                 *   FILE 935
//*     - SQLITE3O - The object code for SQLITE to be bound into    *   FILE 935
//*                  the application.  This was compiled on z/OS    *   FILE 935
//*                  1.13, but the C compiler options were for      *   FILE 935
//*                  compatibility with z/OS 1.11 or higher.        *   FILE 935
//*                                                                 *   FILE 935
//*     - TESTCOB1 - Example Enterprise COBOL program. It is        *   FILE 935
//*                  very basic.  It uses the SQLITE3A stub to      *   FILE 935
//*                  invoke SQLITE3 operations.  If you look at     *   FILE 935
//*                  this, you will realize that I am very          *   FILE 935
//*                  "wordy" and am definitely not an advanced      *   FILE 935
//*                  COBOL programmer.                              *   FILE 935
//*                                                                 *   FILE 935
//*     - TESTCOB2 - Example Enterprise COBOL program. It is        *   FILE 935
//*                  very basic.  It's main example is of how to    *   FILE 935
//*                  retrieve a double precision floating point     *   FILE 935
//*                  number, COMP-2, from an sqlite data base,      *   FILE 935
//*                  converting it from BFP to HFP format.  The     *   FILE 935
//*                  table is defined like:  CREATE TABLE xz        *   FILE 935
//*                  (int INTEGER, fd DOUBLE); This table can be    *   FILE 935
//*                  created and loaded from a z/OS UNIX shell      *   FILE 935
//*                  prompt using the "sqlite3" UNIX command.       *   FILE 935
//*                                                                 *   FILE 935
//*                  From my testing, it appears that sqlite,       *   FILE 935
//*                  unlike most other RDMS systems is case         *   FILE 935
//*                  sensitive for the names of objects such as     *   FILE 935
//*                  columns and tables.                            *   FILE 935
//*                                                                 *   FILE 935
//*     - TESTDB   - A pax archive which contains the               *   FILE 935
//*                  testdb.sqlite3 UNIX file used by the           *   FILE 935
//*                  TESTCOB2 program. This needs to be restored    *   FILE 935
//*                  to a subdirectory (/tmp in the example         *   FILE 935
//*                  code). This can be done with a UNIX command    *   FILE 935
//*                  sequence such as:                              *   FILE 935
//*                                                                 *   FILE 935
//*                  cd /tmp                                        *   FILE 935
//*                  pax -rzf "//sql3811.cntl(testdb)"              *   FILE 935
//*                                                                 *   FILE 935
//*     - TSTPLI1  - Example Enterprise PL/I program. It is very    *   FILE 935
//*                  basic.  It is functionally equivalent to       *   FILE 935
//*                  TESTCOB1.  This is not working at present.     *   FILE 935
//*                  I don't normally program in PL/I because I     *   FILE 935
//*                  have never worked in a shop which used it.     *   FILE 935
//*                                                                 *   FILE 935
//*     - UNPAX    - The JCL needed to unwind either the PAXFULL    *   FILE 935
//*                  or PAXRUN member into a z/OS UNIX              *   FILE 935
//*                  subdirectory.                                  *   FILE 935
//*                                                                 *   FILE 935
//*     Notes on the SQLITE3A interface program.                    *   FILE 935
//*     ----------------------------------------                    *   FILE 935
//*                                                                 *   FILE 935
//*     Notes on how to use SQLITE3A with COBOL.                    *   FILE 935
//*     ----------------------------------------                    *   FILE 935
//*     1. One major oddity which can be quite confusing is that    *   FILE 935
//*        the C language calling sequence is quite different       *   FILE 935
//*        from the normal COBOL calling sequence. This             *   FILE 935
//*        difference is why many of the parameters being passed    *   FILE 935
//*        to sqlite3 _must_ be BY VALUE instead of BY REFERENCE.   *   FILE 935
//*        In particular things such as integers (COMP-?) and       *   FILE 935
//*        doubles must be passed BY VALUE.  Character strings      *   FILE 935
//*        are passed BY REFERENCE, but generally require that      *   FILE 935
//*        they be terminated with by LOW-VALUES byte. In modern    *   FILE 935
//*        COBOLs, this type of string can be initialized using     *   FILE 935
//*        the Z'value' construct.                                  *   FILE 935
//*                                                                 *   FILE 935
//*     2. When reading the C API documentation, the main thing     *   FILE 935
//*        to remember is that when you see a varible prefixed      *   FILE 935
//*        with an ampersand, you need to pass it BY REFERENCE.     *   FILE 935
//*        If you see a variable suffixed with an asterisk, this    *   FILE 935
//*        indicates that the variable name in COBOL needs to be    *   FILE 935
//*        defined as USAGE POINTER. This is also passed BY         *   FILE 935
//*        VALUE.                                                   *   FILE 935
//*                                                                 *   FILE 935
//*     3. Another critical point is that when you see a function   *   FILE 935
//*        return an "int", which is a 32 bit integer, or a name    *   FILE 935
//*        prefixed by an asterisk, you receive these into a        *   FILE 935
//*        COBOL variable by using the phrase RETURNING             *   FILE 935
//*        cobol-var-name.  All other types of returned             *   FILE 935
//*        variables, such as double floating point, are received   *   FILE 935
//*        by inserting a BY REFERENCE phrase as the second         *   FILE 935
//*        parameter in the CALL verb's USING clause.  Please       *   FILE 935
//*        review TESTCOB2 for an example.                          *   FILE 935
//*                                                                 *   FILE 935
//*     4. Unlike most z/OS languages, sqlite stores floating       *   FILE 935
//*        point numbers in IEEE 754 format. On the z, this is      *   FILE 935
//*        called a BFP or Binary Floating Point number. After      *   FILE 935
//*        retrieving a BFP number from sqlite, you will likely     *   FILE 935
//*        need to call the function "CONVERT-BFP-TO-HFP".          *   FILE 935
//*        Likewise, before sending a value to sqlite, you need     *   FILE 935
//*        to convert it to BFP by calling the                      *   FILE 935
//*        "CONVERT-HFP-TO-BFP" function.  The former is shown      *   FILE 935
//*        in the TESTCOB2 example.  My suggestion is to define     *   FILE 935
//*        BFP number in your COBOL as COMP-2 variables, perhaps    *   FILE 935
//*        suffixed with -BFP. Use COMP-2 for your COBOL            *   FILE 935
//*        floating point number also, not COMP-1. Both of these    *   FILE 935
//*        formats use an 8 bytes for storage.                      *   FILE 935
//*                                                                 *   FILE 935
//*     Notes on how to use SQLITE3A with PL/I.                     *   FILE 935
//*     ---------------------------------------                     *   FILE 935
//*     No notes at present. Mainly due to my lack of PL/I skills.  *   FILE 935
//*                                                                 *   FILE 935
//*                                                                 *   FILE 935
//*     Note: This member is in "markdown" compatible format.       *   FILE 935
//*     For more information on markdown, go to                     *   FILE 935
//*     http://daringfireball.net/projects/markdown/                *   FILE 935
//*                                                                 *   FILE 935
```
