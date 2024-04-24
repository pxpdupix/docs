
1. Register an account [![](https://profile.oracle.com/favicon.ico)Oracle | Create Account](https://profile.oracle.com/myprofile/account/create-account.jspx)
    
2. Go to [![](https://container-registry.oracle.com/i/favicon-16x16.png)Home](https://container-registry.oracle.com/)
    
    1. Select Database
        
        ![[Pasted image 20240309195514.png]]
        
    2. select enterprise Repository
        
        ![[Pasted image 20240309195524.png]]
        
    3. sign in and accept EULA
        
        ![[Pasted image 20240309195544.png]]
        
3. Login with docker
    
    `docker login container-registry.oracle.com`
    
4. Pull image
    
    `docker pull container-registry.oracle.com/database/enterprise:19.3.0.0`
    
5. Run container
    
    `docker run -d --name astdb -p 1521:1521 -e ORACLE_PWD=ast -e ORACLE_SID=ast container-registry.oracle.com/database/enterprise:19.3.0.0`
    
6. Monitor installation
    
    `docker logs -f astdb`
    
    When you see this in the log, that means the container is ready to use
    
    `DONE: Executing user defined scripts The Oracle base remains unchanged with value /opt/oracle ######################### DATABASE IS READY TO USE! ######################### The following output is now a tail of the alert.log: ORCLPDB1(3): ORCLPDB1(3):XDB initialized. 2023-05-26T10:11:56.147085+00:00 ALTER SYSTEM SET control_files='/opt/oracle/oradata/AST/control01.ctl' SCOPE=SPFILE; 2023-05-26T10:11:56.155974+00:00 ALTER SYSTEM SET local_listener='' SCOPE=BOTH; ALTER PLUGGABLE DATABASE ORCLPDB1 SAVE STATE Completed: ALTER PLUGGABLE DATABASE ORCLPDB1 SAVE STATE XDB initialized.`
    
7. Enter the container
    
    `docker exec -it astdb bash -c "source /home/oracle/.bashrc; sqlplus /nolog"`
    
8. Create user
    
    1. log in as sysdba
        
        `connect sys as sysdba;`
        
        password: Oradoc_bd1
        
    2. Create user ast
        
        `alter session set "_ORACLE_SCRIPT"=true; create user ast identified by ast; GRANT ALL PRIVILEGES TO ast;`
        
9. Configure Oracle for XA
    
    `grant execute on dbms_xa to ast; grant select on pending_trans$ to ast; grant select on dba_2pc_pending to ast; grant select on dba_pending_transactions to ast;`
    
10. Obtain container IP
    
    1. Obtain container ID
        
        `docker ps`
        
    2. Get container ip
        
        `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER_ID]`