---
layout: post
title: Oracle - KeyStore Management in PDB
modified: 2019-03-23
categories: [Oracle]
tags: 
  - Oracle.Configure
comments: true
---

I am testing to use wallet to encrypt the rman backup. It is a little hard to manage wallet in PDB environment with password. Auto_login is easier.
<pre class="prettyprint lang-sql linenums=1 ">
-- wallet
$ cat /opt/oracle/product/12.2/dbhome_1/network/admin/sqlnet.ora 
ENCRYPTION_WALLET_LOCATION=
(SOURCE=
  (METHOD=FILE)
  (METHOD_DATA=(DIRECTORY=/opt/oracle/product/12.2/dbhome_1/owm/wallet))
)

WALLET_LOCATION =
(SOURCE =
  (METHOD = FILE)
  (METHOD_DATA=(DIRECTORY=/opt/oracle/product/12.2/dbhome_1/network/wallet))
)

SQLNET.WALLET_OVERRIDE=TRUE
SSL_CLIENT_AUTHENTICATION = FALSE
SSL_VERSION = 0

-- I have pdb$seed and 1 more pdb
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 HR                             READ WRITE NO 

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
NOT_AVAILABLE

-- create wallet         
SQL> ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/opt/oracle/product/12.2/dbhome_1/owm/wallet' IDENTIFIED BY "oracle123";

keystore altered.

-- only p12 file, no sso file, which means it is password managed wallet.
[oracle@testenv wallet]$ ls -l
total 4
-rw-------. 1 oracle oinstall 2408 Mar 22 07:49 ewallet.p12

-- the status is CLOSED, we need to open it.
SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
CLOSED

SQL> administer key management set keystore open identified by "oracle123" CONTAINER=all;

keystore altered.

-- There is no master key inside, so encrypted backup is not working until now
SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN_NO_MASTER_KEY

SQL> alter session set container=hr;

Session altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN_NO_MASTER_KEY

SQL>  alter session set container=pdb$seed;

Session altered.

-- the keystore of PDB$SEED cannot be opened
SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
CLOSED

-- open it separately 
SQL> administer key management set keystore open identified by "oracle123";                    

keystore altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN_NO_MASTER_KEY

-- go back to CDB to create master key.
SQL> alter session set container=CDB$ROOT;

Session altered.

-- An error reported, not all PDB has been set the master key.
SQL> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY "oracle123" WITH BACKUP CONTAINER=ALL;
ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY "oracle123" WITH BACKUP CONTAINER=ALL
*
ERROR at line 1:
ORA-46665: master keys not activated for all PDBs during REKEY

-- CDB has master key
SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN

-- but PDB hr doesn’t have.
SQL> alter session set container=hr;

Session altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN_NO_MASTER_KEY

-- Set master key, an error reported but the key has been set.
SQL> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY "oracle123" WITH BACKUP;
ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY "oracle123" WITH BACKUP
*
ERROR at line 1:
ORA-28374: typed master key not found in wallet


SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN

-- PDB$SEED is OK
SQL> alter session set container=pdb$seed;

Session altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN

-- Now schedule a backup, it is successful.

-- after restart database, the wallet cannot be open automatically.
SQL> startup force;
ORACLE instance started.

Total System Global Area 1073741824 bytes
Fixed Size                  8801008 bytes
Variable Size             373294352 bytes
Database Buffers          683671552 bytes
Redo Buffers                7974912 bytes
Database mounted.
Database opened.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
CLOSED

-- open the wallet
SQL> administer key management set keystore open identified by "oracle123" CONTAINER=all;

keystore altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN

SQL> alter session set container=hr;

Session altered.

SQL>  select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN

-- CDB and PDB hr is open, but PDB$SEED isn’t.
SQL> alter session set container=pdb$seed;

Session altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
CLOSED

-- open it
SQL> administer key management set keystore open identified by "oracle123";

keystore altered.

SQL> select STATUS from V$ENCRYPTION_WALLET;

STATUS
------------------------------
OPEN


-- switch to auto_login
SQL> alter session set container=cdb$root;

Session altered.

SQL> ADMINISTER KEY MANAGEMENT CREATE AUTO_LOGIN KEYSTORE FROM KEYSTORE '/opt/oracle/product/12.2/dbhome_1/owm/wallet' IDENTIFIED BY "oracle123";

keystore altered.

SQL> select status, WALLET_TYPE from V$ENCRYPTION_WALLET;

STATUS                         WALLET_TYPE
------------------------------ --------------------
OPEN                           PASSWORD

-- The key is cached, we need to close it until it is opened next time automatically.
SQL> administer key management set keystore close identified by "oracle123"  CONTAINER=all;

keystore altered.

-- start a backup, we can see the wallet is opened automatically.
SQL> select status, WALLET_TYPE from V$ENCRYPTION_WALLET;

STATUS                         WALLET_TYPE
------------------------------ --------------------
OPEN                           AUTOLOGIN

-- for auto login wallet, even orapki doesn’t need password.
[oracle@testenv wallet]$ orapki wallet display -wallet . 
Oracle PKI Tool : Version 12.2.0.1.0
Copyright (c) 2004, 2016, Oracle and/or its affiliates. All rights reserved.

Requested Certificates: 
Subject:        CN=oracle
User Certificates:
Oracle Secret Store entries: 
ORACLE.SECURITY.DB.ENCRYPTION.AfOsN1J/RU+ev9oKzlrKAq4AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
ORACLE.SECURITY.DB.ENCRYPTION.ART74BrBKU9kv8HSwg86xa8AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
ORACLE.SECURITY.DB.ENCRYPTION.AVQR9Olj2U98v0vzbiE1Q/gAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
ORACLE.SECURITY.DB.ENCRYPTION.MASTERKEY
ORACLE.SECURITY.DB.ENCRYPTION.MASTERKEY.82BCD64B5E077D62E05329101053F341
ORACLE.SECURITY.ID.ENCRYPTION.
ORACLE.SECURITY.KB.ENCRYPTION.
ORACLE.SECURITY.KM.ENCRYPTION.AfOsN1J/RU+ev9oKzlrKAq4AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
ORACLE.SECURITY.KM.ENCRYPTION.ART74BrBKU9kv8HSwg86xa8AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
ORACLE.SECURITY.KM.ENCRYPTION.AVQR9Olj2U98v0vzbiE1Q/gAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Trusted Certificates: 
</pre>

If you need to switch backup to password mode, refer this document.<br/>
    How to convert Local Autologin/Autologin Keystore to Password based Keystore (Doc ID 2061244.1)	



